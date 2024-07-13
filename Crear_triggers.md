# Crear-triggers
## 1. Crear un función y un trigger para validar que el numero de cedula del cliente tenga 10 números (no letras) en la tabla cliente.
### Crear la función de validación
 - Sentencia:
```
CREATE OR REPLACE FUNCTION validar_cedula()
RETURNS TRIGGER AS $$
BEGIN

    IF LENGTH(NEW.cedula) != 10 OR NEW.cedula !~ '^[0-9]+$' THEN
        RAISE EXCEPTION 'El número de cédula debe tener exactamente 10 dígitos.';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
````
-Captura.
<imag!![image](https://github.com/user-attachments/assets/660a5dec-ecdd-430a-a082-693093f3a630)
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

<imag!![image](https://github.com/user-attachments/assets/54109edd-0ba6-4a6d-88b9-b43f24676236)
### Crear el trigger.
 - Sentencia:
```
CREATE TRIGGER trigger_disminuir_stock
AFTER INSERT ON item
FOR EACH ROW
EXECUTE PROCEDURE disminuir_stock();
````
-Captura.

<imag!![image](https://github.com/user-attachments/assets/0f0c1cd6-28d4-4e84-997f-091e690d9be6)
