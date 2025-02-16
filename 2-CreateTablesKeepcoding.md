## Create tables for the EDR created

``` sql
-- Crear la tabla Alumnos
CREATE TABLE Alumnos (
    dni VARCHAR(20) PRIMARY KEY,
    nombre VARCHAR(100),
    apellido VARCHAR(100),
    bootcamp_id INT,
    FOREIGN KEY (bootcamp_id) REFERENCES Bootcamps(bootcamp_id)
);

-- Crear la tabla Bootcamps
CREATE TABLE Bootcamps (
    bootcamp_id INT PRIMARY KEY,
    nombre VARCHAR(100),
    descripcion TEXT
);

-- Crear la tabla Modulos
CREATE TABLE Modulos (
    modulo_id INT PRIMARY KEY,
    nombre VARCHAR(100),
    descripcion TEXT,
    bootcamp_id INT,
    FOREIGN KEY (bootcamp_id) REFERENCES Bootcamps(bootcamp_id)
);

-- Crear la tabla Profesores
CREATE TABLE Profesores (
    profesor_id INT PRIMARY KEY,
    nombre VARCHAR(100),
    apellido VARCHAR(100),
    modulo_id INT,
    FOREIGN KEY (modulo_id) REFERENCES Modulos(modulo_id)
);
```

## Explanation

This is a simple ERD where the bootcamp table is the "most important" one since it's the one that connects with almost all the tables besides the teachers one since it's connected to the modules which is connected to the bootcamps