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


## 2. 
