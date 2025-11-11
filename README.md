# 📘 DOCUMENTACIÓN DEL PROYECTO
## Sistema de Gestión Empresarial – Tech Solutions
### Microsoft Visual Studio .NET + WPF + C# + SQL Server

---

## 1. Introducción

La empresa **Tech Solutions** se dedica a la venta y distribución de productos tecnológicos. Actualmente enfrenta problemas en la gestión de clientes, productos y ventas debido a un sistema obsoleto y poco escalable. Esto afecta la toma de decisiones y genera inconsistencias en el stock y reportes.

El presente proyecto desarrolla una **Aplicación Empresarial** utilizando Visual Studio .NET, aplicando el **Patrón de Arquitectura en N-Capas**, integración con SQL Server, autenticación segura y gestión de reportes.

---

## 2. Objetivo General

Implementar un sistema que permita la gestión eficiente de clientes, productos y ventas en Tech Solutions, asegurando integridad de datos, manteniendo escalabilidad y cumpliendo buenas prácticas de desarrollo empresarial.

---

## 3. Objetivos Específicos

- ✅ Aplicar el patrón N-Capas para lograr modularidad y mantenibilidad.
- ✅ Implementar autenticación y autorización basada en roles.
- ✅ Asegurar la integridad de registros mediante transacciones en ventas y stock.
- ✅ Gestionar productos, categorías, proveedores y clientes.
- ✅ Permitir la generación de reportes (ventas, inventario, productos vendidos).
- ✅ Implementar interfaz de usuario profesional utilizando WPF.
- ✅ Generar un paquete de instalación para distribución del sistema.

---

## 4. Requerimientos Funcionales

| Código | Requerimiento | Descripción |
|--------|---------------|-------------|
| RF01 | Gestión de Usuarios | Registro, edición y autenticación con roles. |
| RF02 | Gestión de Productos | Registrar productos y actualizar stock. |
| RF03 | Gestión de Clientes | Registro y mantenimiento de clientes. |
| RF04 | Registro de Ventas | Realiza ventas, descuenta stock y registra detalle. |
| RF05 | Reportes | Generar reportes de ventas y productos. |

---

## 5. Requerimientos No Funcionales

| Tipo | Requisito |
|------|-----------|
| Seguridad | Hash de contraseñas con SHA-256. |
| Integridad | Uso de transacciones SQL + rollback. |
| Usabilidad | Interfaz amigable WPF. |
| Escalabilidad | Arquitectura modular en N-capas. |

---

## 6. Arquitectura del Sistema (Patrón N-Capas)

```
TechSolutions.sln   ← (Solución)
│
├── CapaEntidad          ← Modelos (clases que representan tablas)
│   └── Models
│       ├── Usuario.cs
│       ├── Cliente.cs
│       ├── Proveedor.cs
│       ├── Categoria.cs
│       ├── Producto.cs
│       ├── Venta.cs
│       └── DetalleVenta.cs
│
├── CapaDatos            ← DAL (Acceso a SQL Server, ADO.NET)
│   ├── Database
│   │   └── Conexion.cs        ← Clase Singleton
│   └── Repositorio
│       ├── UsuarioDAL.cs
│       ├── ClienteDAL.cs
│       ├── ProductoDAL.cs
│       ├── VentaDAL.cs
│       └── ReporteDAL.cs      ← Opcional si manejas reportes vía SP
│
├── CapaNegocio          ← BLL (Reglas de negocio)
│   ├── Servicios
│   │   ├── UsuarioBLL.cs
│   │   ├── ClienteBLL.cs
│   │   ├── ProductoBLL.cs
│   │   ├── VentaBLL.cs
│   │   └── ReporteBLL.cs
│   └── Seguridad
│       └── PasswordHasher.cs   ← Hash SHA-256
│
└── CapaPresentacion      ← WPF (UI)
    ├── Forms
    │   ├── Login.xaml
    │   ├── MenuPrincipal.xaml
    │   ├── ClientesForm.xaml
    │   ├── ProductosForm.xaml
    │   ├── VentasForm.xaml
    │   └── ReportesForm.xaml
    └── Reportes
        └── (RDLC o controladores de exportación)
```

### Descripción de Capas

| Capa | Responsabilidad |
|------|----------------|
| CapaEntidad | Contiene las clases que representan las tablas de la BD. |
| CapaAccesoDatos (DAL) | Maneja la conexión a SQL Server y ejecución de consultas. |
| CapaNegocio (BLL) | Aplica reglas de negocio y validaciones. |
| CapaPresentacion (WPF) | Interfaz de usuario para interacción con el sistema. |

### Flujo de ejecución
```
Usuario → WPF (UI) → BLL → DAL → SQL Server
```

---

## 7. Modelo de Datos (Entidad-Relación)

**Tablas principales:**
- Roles
- Usuarios
- Clientes
- Proveedores
- Categorias
- Productos
- TipoMovimiento
- TransaccionesStock
- Ventas
- DetalleVenta

**Relaciones importantes:**
- Producto pertenece a Categoría y Proveedor
- Venta es realizada por Usuario a Cliente
- DetalleVenta contiene los productos vendidos
- TransaccionesStock registra entradas y salidas del inventario
- TipoMovimiento clasifica los movimientos de stock

---

## 8. Modelos (CapaEntidad)

### Rol.cs
```csharp
namespace CapaEntidad.Models
{
    public class Rol
    {
        public int IdRol { get; set; }
        public string NombreRol { get; set; }
        public string Descripcion { get; set; }
    }
}
```

### Usuario.cs
```csharp
namespace CapaEntidad.Models
{
    public class Usuario
    {
        public int IdUsuario { get; set; }
        public string NombreUsuario { get; set; }
        public byte[] ContrasenaHash { get; set; }
        public int IdRol { get; set; }
        public bool Estado { get; set; }

        public Rol Rol { get; set; }  // Opcional (relación)
    }
}
```

### Cliente.cs
```csharp
namespace CapaEntidad.Models
{
    public class Cliente
    {
        public int IdCliente { get; set; }
        public string Nombre { get; set; }
        public string Apellido { get; set; }
        public string Email { get; set; }
        public string Telefono { get; set; }
        public string Direccion { get; set; }
    }
}
```

### Proveedor.cs
```csharp
namespace CapaEntidad.Models
{
    public class Proveedor
    {
        public int IdProveedor { get; set; }
        public string NombreProveedor { get; set; }
        public string Telefono { get; set; }
        public string Email { get; set; }
        public string Direccion { get; set; }
    }
}
```

### Categoria.cs
```csharp
namespace CapaEntidad.Models
{
    public class Categoria
    {
        public int IdCategoria { get; set; }
        public string NombreCategoria { get; set; }
        public string Descripcion { get; set; }
    }
}
```

### Producto.cs
```csharp
namespace CapaEntidad.Models
{
    public class Producto
    {
        public int IdProducto { get; set; }
        public string Nombre { get; set; }
        public string Descripcion { get; set; }
        public decimal Precio { get; set; }
        public int Stock { get; set; }
        public int IdCategoria { get; set; }
        public int? IdProveedor { get; set; }

        public Categoria Categoria { get; set; } // Opcional
        public Proveedor Proveedor { get; set; } // Opcional
    }
}
```

### Venta.cs
```csharp
namespace CapaEntidad.Models
{
    public class Venta
    {
        public int IdVenta { get; set; }
        public DateTime Fecha { get; set; }
        public int IdCliente { get; set; }
        public int IdUsuario { get; set; }
        public decimal Total { get; set; }

        public Cliente Cliente { get; set; } // Opcional
        public Usuario Usuario { get; set; } // Opcional
    }
}
```

### DetalleVenta.cs
```csharp
namespace CapaEntidad.Models
{
    public class DetalleVenta
    {
        public int IdDetalleVenta { get; set; }
        public int IdVenta { get; set; }
        public int IdProducto { get; set; }
        public int Cantidad { get; set; }
        public decimal PrecioUnitario { get; set; }
        public decimal Subtotal { get; set; }

        public Venta Venta { get; set; } // Opcional
        public Producto Producto { get; set; } // Opcional
    }
}
```

### TipoMovimiento.cs
```csharp
namespace CapaEntidad.Models
{
    public class TipoMovimiento
    {
        public int IdTipoMovimiento { get; set; }
        public string NombreMovimiento { get; set; }
    }
}
```

### TransaccionStock.cs
```csharp
namespace CapaEntidad.Models
{
    public class TransaccionStock
    {
        public int IdTransaccion { get; set; }
        public int IdProducto { get; set; }
        public int IdTipoMovimiento { get; set; }
        public int Cantidad { get; set; }
        public DateTime FechaMovimiento { get; set; }
        public string Observacion { get; set; }

        public Producto Producto { get; set; } // Opcional
        public TipoMovimiento TipoMovimiento { get; set; } // Opcional
    }
}
```

---

## 9. Conexión y Acceso a Datos (DAL)

### Patrón Singleton:
```csharp
public sealed class ConexionBD
{
    private static readonly ConexionBD _instancia = new ConexionBD();
    public static ConexionBD Instancia => _instancia;
    private ConexionBD() { }

    private readonly string cadena = "Data Source=.;Initial Catalog=TechSolutionsDB;Integrated Security=True";
    public SqlConnection CrearConexion() => new SqlConnection(cadena);
}
```

### Repositorios:
- UsuarioDAL.cs
- ProductoDAL.cs
- VentaDAL.cs

---

## 10. Lógica de Negocio (BLL)

Aquí se valida y controla la lógica:

```csharp
public class VentaBLL
{
    public bool RegistrarVenta(Venta venta, List<DetalleVenta> detalles)
    {
        // Validaciones → DAL → Transacciones → OK
    }
}
```

---

## 11. Interfaz (WPF)

**Pantallas principales:**
- Login
- Menú principal
- Gestión de Clientes
- Gestión de Productos
- Ventas (con carrito/simple detalle)
- Reportes PDF/Excel

---

## 12. Reporte

**Se utilizarán:**
- RDLC + DataSet desde DAL
- Exportación a PDF / Excel desde ReportViewer

---

## 13. Conclusiones

- ✅ Se logró implementar un sistema modular utilizando N-Capas, asegurando separación adecuada entre negocio, datos y visual.
- ✅ Se mejoró la seguridad mediante hash de contraseñas y control de roles.
- ✅ Las transacciones permiten mantener la integridad del stock y ventas.
- ✅ La solución es escalable, pudiendo conectarse a API, aplicaciones web o móviles en el futuro.

---

## 14. Recomendaciones

- 🔮 Implementar WebAPI para versión multiusuario en red.
- 🔮 Migrar a autenticación JWT si se requiere acceso desde web.
- 🔮 Agregar dashboard con gráficos para toma de decisiones.

---



**Responde:**
1) PNG
2) PowerPoint listo para exponer
