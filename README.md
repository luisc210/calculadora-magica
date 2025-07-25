<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora Mágica</title>
    <link rel="stylesheet" href="styles.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.28/jspdf.plugin.autotable.min.js"></script>
    <style>
        /* Estilos para el mensaje de derechos de autor */
        .copyright-notice {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background-color: #f8f9fa;
            border-left: 5px solid #dc3545;
            padding: 15px;
            max-width: 300px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            border-radius: 5px;
            font-size: 14px;
            z-index: 1000;
            animation: slideIn 0.5s ease-out;
            display: none;
        }
        
        .copyright-notice.show {
            display: block;
        }
        
        .copyright-notice .close-btn {
            position: absolute;
            top: 5px;
            right: 5px;
            background: none;
            border: none;
            font-size: 16px;
            cursor: pointer;
            color: #6c757d;
        }
        
        .copyright-notice strong {
            color: #dc3545;
        }
        
        @keyframes slideIn {
            from { transform: translateX(100%); opacity: 0; }
            to { transform: translateX(0); opacity: 1; }
        }
        
        .copyright-icon {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background-color: #dc3545;
            color: white;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            box-shadow: 0 2px 5px rgba(0,0,0,0.2);
            z-index: 999;
            font-weight: bold;
            font-size: 18px;
        }
    </style>
</head>
<body>
    <!-- Icono flotante de copyright -->
    <div class="copyright-icon" onclick="toggleCopyrightNotice()">©</div>
    
    <!-- Mensaje de derechos de autor -->
    <div class="copyright-notice" id="copyrightNotice">
        <button class="close-btn" onclick="toggleCopyrightNotice()">×</button>
        <strong>ADVERTENCIA:</strong> Este sistema está protegido por derechos de autor. 
        <strong>No revendas tu código de acceso</strong> o será bloqueado y perderás tu dinero. 
        Cualquier uso no autorizado será penalizado según las leyes aplicables.
    </div>

    <div class="calculator">
        <h2>Calculadora Mágica</h2>
        
        <!-- Sección Nombre Establecimiento -->
        <section>
            <h3>Nombre del Establecimiento</h3>
            <input type="text" id="nombreEstablecimiento" placeholder="Ej: Tienda La Bendición">
            <button onclick="guardarNombreEstablecimiento()">Guardar Nombre</button>
        </section>

        <!-- Sección Tasa BCV -->
        <section>
            <h3>Tasa BCV</h3>
            <input type="number" id="tasaBCV" placeholder="Tasa BCV" required="true">
            <button onclick="actualizarTasaBCV()">Actualizar Tasa BCV</button>
        </section>

        <!-- Sección Datos Producto -->
        <section>
            <h3>Datos del Producto</h3>
            <input type="text" id="producto" placeholder="Nombre del producto" required="true">
            <input type="number" id="costo" placeholder="Costo del producto" required="true">
            <select id="descripcion" required="true">
                <option value="">Selecciona una descripción</option>
                <option value="viveres">Víveres</option>
                <option value="gaseosas">Gaseosas</option>
                <option value="licores">Licores</option>
                <option value="enlatados">Enlatados</option>
                <option value="plasticos">Plásticos</option>
                <option value="papeleria">Papelería</option>
                <option value="lacteos">Lácteos</option>
                <option value="ferreteria">Ferretería</option>
                <option value="agropecuaria">Agropecuaria</option>
                <option value="frigorifico">Frigorífico</option>
                <option value="pescaderia">Pescadería</option>
                <option value="repuesto">Repuesto</option>
                <option value="confiteria">Confitería</option>
                <option value="ropa">Ropa</option>
                <option value="calzados">Calzados</option>
                <option value="otros">Otros</option>
            </select>
        </section>

        <!-- Sección Precio Venta -->
        <section>
            <h3>Precio de Venta</h3>
            <input type="number" id="ganancia" placeholder="Porcentaje de ganancia" required="true">
            <input type="number" id="unidades" placeholder="Unidades que trae la caja" required="true">
            <button onclick="calcularPrecioVenta()">Calcular Precio de Venta</button>
            <p id="resultadoPrecioVenta">Precio al mayor: </p>
            <p id="precioUnitario">Precio unitario: </p>
        </section>

        <button onclick="guardarProducto()">Guardar Producto</button>
    </div>

    <!-- Lista de Productos -->
    <div id="listaProductos">
        <input type="text" id="buscar" placeholder="Buscar producto">
        <button onclick="buscarProducto()">Buscar</button>
        
        <table>
            <thead>
                <tr>
                    <th>Producto</th>
                    <th>Descripción</th>
                    <th>Precio Mayor ($)</th>
                    <th>Precio Mayor (Bs)</th>
                    <th>Precio Unitario ($)</th>
                    <th>Precio Unitario (Bs)</th>
                    <th>Acciones</th>
                </tr>
            </thead>
            <tbody></tbody>
        </table>

        <!-- Botones de Acción -->
        <div class="action-buttons">
            <button onclick="generarPDF()" class="generar-pdf">Generar PDF</button>
            <button onclick="mostrarListaCostos()" class="lista-costos">Mostrar Lista de Costos</button>
            <button onclick="cerrarSesion()" class="btn-cerrar-sesion">✅ Cerrar Sesión</button>
            <button onclick="limpiarLista()" class="limpiar-lista">Limpiar Lista</button>
        </div>

        <!-- Lista de Costos (oculta por defecto) -->
        <div id="listaCostosContainer" style="display: none;">
            <h3>Lista de Costos de Productos</h3>
            <button onclick="generarPDFCostos()" class="generar-pdf-costos">📄 Convertir a PDF</button>
            <ul id="listaCostos"></ul>
        </div>
    </div>

    <script>
        // Función para mostrar/ocultar el mensaje de copyright
        function toggleCopyrightNotice() {
            const notice = document.getElementById('copyrightNotice');
            notice.classList.toggle('show');
        }
        
        // Mostrar el mensaje automáticamente al cargar la página (después de 5 segundos)
        setTimeout(() => {
            toggleCopyrightNotice();
            // Ocultar después de 15 segundos
            setTimeout(() => {
                const notice = document.getElementById('copyrightNotice');
                if (notice.classList.contains('show')) {
                    notice.classList.remove('show');
                }
            }, 15000);
        }, 5000);
    </script>
    
    <script src="script.js"></script>
</body>
</html>
