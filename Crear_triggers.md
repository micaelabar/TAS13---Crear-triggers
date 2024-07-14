# Crear-triggers
## 1. Crear un función y un trigger para validar que el numero de cedula del cliente tenga 10 números (no letras) en la tabla cliente.
### Crear la función de validación
 - Sentencia:
```
CREATE OR REPLACE FUNCTION validate_cedula()
RETURNS TRIGGER AS $$
BEGIN

    IF LENGTH(NEW.cedula) != 10 OR NEW.cedula !~ '^[0-9]+$' THEN
        RAISE EXCEPTION 'The ID number must have exactly 10 digits.';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
````
-Captura.

<imag!![image](https://github.com/user-attachments/assets/3f1fc66d-5177-47ea-b599-77ccb9883119)

### Crear el trigger que llama a la función.
 - Sentencia:
```
CREATE TABLE cliente (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    NUI VARCHAR(10)
);
````
-Captura.

<imag!![image](https://github.com/user-attachments/assets/3e7ebdc4-1161-4e5a-b3d0-08e703cfe8bb)
## 2. Crear un función y un trigger para que cada vez que se inserte un nuevo registro en la tabla item se disminuya el stock de la tabla product.
### Crear la función de actualización del stock.
 - Sentencia:
```
CREATE OR REPLACE FUNCTION disminuir_stock()
RETURNS TRIGGER AS $$
BEGIN
   
    UPDATE product
    SET stock = stock - NEW.quantity
    WHERE id = NEW.product_id;

    IF (SELECT stock FROM product WHERE id = NEW.product_id) < 0 THEN
        RAISE EXCEPTION 'Stock insuficiente para el producto con id %', NEW.product_id;
    END IF;

    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
````
-Captura.

<imag!![image](https://github.com/user-attachments/assets/242830e7-5c33-42e1-a388-9bc2b047b375)

### Crear el trigger.
 - Sentencia:
```
CREATE TRIGGER trigger_decrease_stock
AFTER INSERT ON item
FOR EACH ROW
EXECUTE PROCEDURE decrease_stock();
````
-Captura.

<imag!![image](https://github.com/user-attachments/assets/36c2e94b-e701-49b3-9a60-a76588b88041)

## 3. Crear un función y un trigger para la tabla invoice donde valide que el campo create_at sea del año actual (fecha sistema).
### Crear la función de validación.
 - Sentencia:
```
CREATE OR REPLACE FUNCTION validate_current_date()
RETURNS TRIGGER AS $$
BEGIN
   
    IF EXTRACT(YEAR FROM NEW.create_at) != EXTRACT(YEAR FROM CURRENT_DATE) THEN
        RAISE EXCEPTION 'The create_at date must be in the current year.';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
````
-Captura.

<imag!![image](https://github.com/user-attachments/assets/9c98a43b-07fc-4f39-ac19-c74c7ea8b65b)

### Crear el trigger.
 - Sentencia:
```
CREATE TRIGGER trigger_validate_current_date
BEFORE INSERT ON invoice
FOR EACH ROW
EXECUTE PROCEDURE validate_current_date();
````
-Captura.

<imag!![image](https://github.com/user-attachments/assets/c175261b-1d11-4596-b401-7178a16ed345)

## 4. Crear un función y un trigger para la tabla client y validar que el correo tenga un @.
### Crear la función de validación.
 - Sentencia:
```
CREATE OR REPLACE FUNCTION validate_mail()
RETURNS TRIGGER AS $$
BEGIN
    -- Verify that the email field contains an '@'
    IF POSITION('@' IN NEW.email) = 0 THEN
        RAISE EXCEPTION 'Email must contain an @';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
````
-Captura.

<imag!![image](https://github.com/user-attachments/assets/a7ab97cd-3a33-42f2-b69f-173f2c510652)

###  Crear el trigger que llama a la función.
 - Sentencia:
```
CREATE TABLE client (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);
````
-Captura.

<imag![image](https://github.com/user-attachments/assets/4c7ef184-a16c-4b0d-8767-61917ec310e9)



