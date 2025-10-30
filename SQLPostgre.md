CREATE TABLE Usuarios (
   id_usuario SERIAL PRIMARY KEY,
   nombre VARCHAR(100) NOT NULL,
   apellido VARCHAR(100) NOT NULL,
   email VARCHAR(255) UNIQUE NOT NULL,
   contrasena TEXT NOT NULL,
   rol VARCHAR(50) NOT NULL,
   fecha_registro DATE NOT NULL DEFAULT CURRENT_DATE
);

CREATE TABLE Destinos (
   id_destino SERIAL PRIMARY KEY,
   nombre VARCHAR(150) UNIQUE NOT NULL,
   descripcion TEXT, -- corregido: description → descripcion
   precio_base NUMERIC(10, 2) NOT NULL,
   ubicacion VARCHAR(255),
   categoria VARCHAR(50)
);

CREATE TABLE Promociones (
   id_promocion SERIAL PRIMARY KEY,
   titulo VARCHAR(255) NOT NULL,
   descripcion TEXT, -- corregido: description → descripcion
   descuento NUMERIC(5, 2) NOT NULL,
   fecha_inicio DATE NOT NULL,
   fecha_fin DATE NOT NULL,
   CHECK (fecha_fin >= fecha_inicio)
);

CREATE TABLE Reportes (
   id_reporte SERIAL PRIMARY KEY,
   tipo VARCHAR(100) NOT NULL,
   fecha_generacion TIMESTAMP WITHOUT TIME ZONE NOT NULL DEFAULT NOW(), -- corregido: generation → generacion
   datos_json JSONB
);

CREATE TABLE Logs (
   id_log SERIAL PRIMARY KEY,
   id_usuario INTEGER,
   accion VARCHAR(255) NOT NULL,
   fecha_hora TIMESTAMP WITHOUT TIME ZONE NOT NULL DEFAULT NOW(),
   detalle TEXT,
   FOREIGN KEY (id_usuario) REFERENCES Usuarios (id_usuario) ON DELETE SET NULL
);

CREATE TABLE PaquetesTuristicos (
   id_paqueteturistico SERIAL PRIMARY KEY, -- corregido: id_paqueteturist
   id_destino INTEGER NOT NULL,
   nombre VARCHAR(255) NOT NULL,
   descripcion TEXT,
   fecha_inicio DATE NOT NULL,
   fecha_fin DATE NOT NULL,
   precio NUMERIC(10, 2) NOT NULL,
   cupos_disponibles INTEGER NOT NULL DEFAULT 0,
   FOREIGN KEY (id_destino) REFERENCES Destinos (id_destino) ON DELETE RESTRICT,
   CHECK (fecha_fin >= fecha_inicio),
   CHECK (precio > 0),
   CHECK (cupos_disponibles >= 0)
);

CREATE TABLE Reservas (
   id_reserva SERIAL PRIMARY KEY,
   id_usuario INTEGER NOT NULL,
   id_paqueteturistico INTEGER NOT NULL, -- corregido: id_pagueteturist
   fecha_reserva DATE NOT NULL DEFAULT CURRENT_DATE,
   estado VARCHAR(50) NOT NULL DEFAULT 'Pendiente', -- agregado valor por defecto
   FOREIGN KEY (id_usuario) REFERENCES Usuarios (id_usuario) ON DELETE RESTRICT,
   FOREIGN KEY (id_paqueteturistico) REFERENCES PaquetesTuristicos (id_paqueteturistico) ON DELETE RESTRICT
);

CREATE TABLE Pagos (
   id_pago SERIAL PRIMARY KEY,
   id_reserva INTEGER UNIQUE NOT NULL,
   monto NUMERIC(10, 2) NOT NULL,
   metodo_pago VARCHAR(100) NOT NULL,
   fecha_pago TIMESTAMP WITHOUT TIME ZONE NOT NULL DEFAULT NOW(),
   estado_pago VARCHAR(50) NOT NULL DEFAULT 'Pendiente',
   FOREIGN KEY (id_reserva) REFERENCES Reservas (id_reserva) ON DELETE RESTRICT,
   CHECK (monto > 0)
);
