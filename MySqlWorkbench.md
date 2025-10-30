CREATE TABLE Usuarios (
   id_usuario INT AUTO_INCREMENT PRIMARY KEY,
   nombre VARCHAR(100) NOT NULL,
   apellido VARCHAR(100) NOT NULL,
   email VARCHAR(255) UNIQUE NOT NULL,
   contrasena TEXT NOT NULL,
   rol VARCHAR(50) NOT NULL,
   fecha_registro DATE NOT NULL DEFAULT (CURRENT_DATE)
);

CREATE TABLE Destinos (
   id_destino INT AUTO_INCREMENT PRIMARY KEY,
   nombre VARCHAR(150) UNIQUE NOT NULL,
   descripcion TEXT,
   precio_base DECIMAL(10,2) NOT NULL,
   ubicacion VARCHAR(255),
   categoria VARCHAR(50)
);

CREATE TABLE Promociones (
   id_promocion INT AUTO_INCREMENT PRIMARY KEY,
   titulo VARCHAR(255) NOT NULL,
   descripcion TEXT,
   descuento DECIMAL(5,2) NOT NULL,
   fecha_inicio DATE NOT NULL,
   fecha_fin DATE NOT NULL,
   CHECK (fecha_fin >= fecha_inicio)
);

CREATE TABLE Reportes (
   id_reporte INT AUTO_INCREMENT PRIMARY KEY,
   tipo VARCHAR(100) NOT NULL,
   fecha_generacion TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
   datos_json JSON
);

CREATE TABLE Logs (
   id_log INT AUTO_INCREMENT PRIMARY KEY,
   id_usuario INT,
   accion VARCHAR(255) NOT NULL,
   fecha_hora TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
   detalle TEXT,
   FOREIGN KEY (id_usuario) REFERENCES Usuarios (id_usuario) ON DELETE SET NULL
);

CREATE TABLE PaquetesTuristicos (
   id_paqueteturistico INT AUTO_INCREMENT PRIMARY KEY,
   id_destino INT NOT NULL,
   nombre VARCHAR(255) NOT NULL,
   descripcion TEXT,
   fecha_inicio DATE NOT NULL,
   fecha_fin DATE NOT NULL,
   precio DECIMAL(10,2) NOT NULL,
   cupos_disponibles INT NOT NULL DEFAULT 0,
   FOREIGN KEY (id_destino) REFERENCES Destinos (id_destino) ON DELETE RESTRICT,
   CHECK (fecha_fin >= fecha_inicio),
   CHECK (precio > 0),
   CHECK (cupos_disponibles >= 0)
);

CREATE TABLE Reservas (
   id_reserva INT AUTO_INCREMENT PRIMARY KEY,
   id_usuario INT NOT NULL,
   id_paqueteturistico INT NOT NULL,
   fecha_reserva DATE NOT NULL DEFAULT (CURRENT_DATE),
   estado VARCHAR(50) NOT NULL DEFAULT 'Pendiente',
   FOREIGN KEY (id_usuario) REFERENCES Usuarios (id_usuario) ON DELETE RESTRICT,
   FOREIGN KEY (id_paqueteturistico) REFERENCES PaquetesTuristicos (id_paqueteturistico) ON DELETE RESTRICT
);

CREATE TABLE Pagos (
   id_pago INT AUTO_INCREMENT PRIMARY KEY,
   id_reserva INT UNIQUE NOT NULL,
   monto DECIMAL(10,2) NOT NULL,
   metodo_pago VARCHAR(100) NOT NULL,
   fecha_pago TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
   estado_pago VARCHAR(50) NOT NULL DEFAULT 'Pendiente',
   FOREIGN KEY (id_reserva) REFERENCES Reservas (id_reserva) ON DELETE RESTRICT,
   CHECK (monto > 0)
);
