# TiendaVirtual_Backend

## El back trabaja con la siguiente base de datos;

script para creación de la base de datos en SQL-Server versión 18 

-- se deben configurar las tablas como identidad con id autoincrementable ya que de no ser así puede generar conflictos con el método POST
-- Crear la base de datos
CREATE DATABASE [TiendaVirtual]
ON PRIMARY 
( NAME = N'TiendaVirtual', FILENAME = N'C:\Program Files\Microsoft SQL Server\MSSQL15.SQLEXPRESS\MSSQL\DATA\TiendaVirtual.mdf' , SIZE = 8192KB , MAXSIZE = UNLIMITED, FILEGROWTH = 65536KB )
LOG ON 
( NAME = N'TiendaVirtual_log', FILENAME = N'C:\Program Files\Microsoft SQL Server\MSSQL15.SQLEXPRESS\MSSQL\DATA\TiendaVirtual_log.ldf' , SIZE = 8192KB , MAXSIZE = 2048GB , FILEGROWTH = 65536KB );

USE [TiendaVirtual];
GO

-- Crear las tablas no dependientes primero
CREATE TABLE [dbo].[Categoria](
    [CategoriaID] [int] NOT NULL,
    [Nombre] [nvarchar](100) NULL,
    [Descripcion] [nvarchar](500) NULL,
    [Estado] [bit] NULL,
    PRIMARY KEY CLUSTERED ([CategoriaID] ASC)
);

CREATE TABLE [dbo].[Usuario](
    [UsuarioID] [int] NOT NULL,
    [NombreUsuario] [nvarchar](100) NULL,
    [Contraseña] [nvarchar](100) NULL,
    [Rol] [nvarchar](50) NULL,
    [CorreoElectronico] [nvarchar](255) NULL,
    [Nombres] [nvarchar](100) NULL,
    [Apellidos] [nvarchar](100) NULL,
    [Estado] [bit] NULL,
    PRIMARY KEY CLUSTERED ([UsuarioID] ASC)
);

-- Crear las tablas dependientes
CREATE TABLE [dbo].[Cliente](
    [ClienteID] [int] NOT NULL,
    [Nombre] [nvarchar](200) NULL,
    [CorreoElectronico] [nvarchar](100) NULL,
    [DireccionEnvio] [nvarchar](500) NULL,
    [Estado] [bit] NULL,
    PRIMARY KEY CLUSTERED ([ClienteID] ASC)
);

CREATE TABLE [dbo].[Carrito](
    [CarritoID] [int] NOT NULL,
    [ClienteID] [int] NULL,
    [Estado] [bit] NULL,
    PRIMARY KEY CLUSTERED ([CarritoID] ASC),
    CONSTRAINT [FK_Carrito_Cliente] FOREIGN KEY([ClienteID]) REFERENCES [dbo].[Cliente]([ClienteID])
);

CREATE TABLE [dbo].[MetodoPago](
    [MetodoPagoID] [int] NOT NULL,
    [TipoTarjeta] [nvarchar](50) NULL,
    [UltimosDigitos] [nvarchar](4) NULL,
    [FechaVencimiento] [nvarchar](10) NULL,
    [ClienteID] [int] NULL,
    [Estado] [bit] NULL,
    PRIMARY KEY CLUSTERED ([MetodoPagoID] ASC),
    CONSTRAINT [FK_MetodoPago_Cliente] FOREIGN KEY([ClienteID]) REFERENCES [dbo].[Cliente]([ClienteID])
);

-- Otras tablas dependientes
-- (Incluye las demás tablas aquí)

-- Finalmente, las tablas que no tienen dependencias
CREATE TABLE [dbo].[Producto](
    [ProductoID] [int] NOT NULL,
    [Nombre] [nvarchar](200) NULL,
    [Descripcion] [nvarchar](1000) NULL,
    [Precio] [decimal](10, 2) NULL,
    [Imagen] [nvarchar](500) NULL,
    [CategoriaID] [int] NULL,
    [Estado] [bit] NULL,
    PRIMARY KEY CLUSTERED ([ProductoID] ASC),
    CONSTRAINT [FK_Producto_Categoria] FOREIGN KEY([CategoriaID]) REFERENCES [dbo].[Categoria]([CategoriaID])
);

CREATE TABLE [dbo].[Opinion](
    [OpinionID] [int] NOT NULL,
    [ProductoID] [int] NULL,
    [Puntuacion] [int] NULL,
    [Comentario] [nvarchar](1000) NULL,
    [FechaPublicacion] [datetime] NULL,
    [Estado] [bit] NULL,
    PRIMARY KEY CLUSTERED ([OpinionID] ASC),
    CONSTRAINT [FK_Opinion_Producto] FOREIGN KEY([ProductoID]) REFERENCES [dbo].[Producto]([ProductoID])
);

CREATE TABLE [dbo].[Pedido](
    [PedidoID] [int] NOT NULL,
    [FechaHora] [datetime] NULL,
    [Estado] [nvarchar](50) NULL,
    [Total] [decimal](10, 2) NULL,
    [ClienteID] [int] NULL,
    [Status] [bit] NULL,
    PRIMARY KEY CLUSTERED ([PedidoID] ASC),
    CONSTRAINT [FK_Pedido_Cliente] FOREIGN KEY([ClienteID]) REFERENCES [dbo].[Cliente]([ClienteID])
);

CREATE TABLE [dbo].[DetallePedido](
    [DetallePedidoID] [int] NOT NULL,
    [PedidoID] [int] NULL,
    [ProductoID] [int] NULL,
    [Cantidad] [int] NULL,
    [PrecioUnitario] [decimal](10, 2) NULL,
    [Subtotal] [decimal](10, 2) NULL,
    [Estado] [bit] NULL,
    PRIMARY KEY CLUSTERED ([DetallePedidoID] ASC),
    CONSTRAINT [FK_DetallePedido_Pedido] FOREIGN KEY([PedidoID]) REFERENCES [dbo].[Pedido]([PedidoID]),
    CONSTRAINT [FK_DetallePedido_Producto] FOREIGN KEY([ProductoID]) REFERENCES [dbo].[Producto]([ProductoID])
);

CREATE TABLE [dbo].[HistorialCompras](
    [HistorialComprasID] [int] NOT NULL,
    [ClienteID] [int] NULL,
    [ProductoID] [int] NULL,
    [FechaCompra] [datetime] NULL,
    [TotalGastado] [decimal](10, 2) NULL,
    [Estado] [bit] NULL,
    PRIMARY KEY CLUSTERED ([HistorialComprasID] ASC),
    CONSTRAINT [FK_HistorialCompras_Cliente] FOREIGN KEY([ClienteID]) REFERENCES [dbo].[Cliente]([ClienteID]),
    CONSTRAINT [FK_HistorialCompras_Producto] FOREIGN KEY([ProductoID]) REFERENCES [dbo].[Producto]([ProductoID])
);
