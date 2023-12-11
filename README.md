# MODELO RELACIONAL

<image src="./diagrama_r.png" alt="modelo relacional inicial">

# MODELO ENTIDAD RELACION

<image src="./diagrama_er.png" alt="modelo entidad relacion incial">

# CONSULTAS
**Desde el inciso 1 hasta el inciso 10**

1. Cantidad de Paradas por Ruta

    ```sql
    SELECT 
        R.nombre_ruta as Ruta, 
        COUNT(*) AS cantidad_paradas
    FROM parada_ruta P
    INNER JOIN rutas R ON R.id_ruta = P.id_ruta
    GROUP BY P.id_ruta;
    ```

    | Ruta          | cantidad_paradas |
    |---------------|------------------|
    | Universidades |                7 |
    | Cacique       |                5 |
    | Diamantes     |                3 |
    | Terminal      |                2 |
    | Ciudadela     |                3 |

2. Nombre de las Paradas de la Ruta Universidades

    ```sql
    SELECT DISTINCT E.nombre_estacion as Parada
    FROM parada_ruta P
    INNER JOIN estaciones E ON E.id_estacion = P.id_parada
    WHERE P.id_ruta = 1;
    ```

    | Parada              |
    |---------------------|
    | Colseguros          |
    | Clínica Chicamocha  |
    | Plaza Guarín        |
    | Mega Mall           |
    | UIS                 |
    | UDI                 |
    | Santo Tomás         |


3. Nombres de las Rutas No Programadas

    ```sql
    SELECT R.nombre_ruta AS NO_PROGRAMADAS
    FROM rutas R
    LEFT JOIN recorrido R2 on R.id_ruta = R2.id_ruta
    WHERE R2.id_conductor IS NULL;
    ```

    | NO_PROGRAMADAS |
    |----------------|
    | Cacique        |
    | Prado          |
    | Cabecera       |
    | Ciudadela      |
    | Punta Estrella |
    | Centro Florida |

4. Rutas Programadas sin Conductor Asignado

    ```sql
    SELECT R.nombre_ruta AS Ruta
    FROM rutas R
    INNER JOIN recorrido R2 on R.id_ruta = R2.id_ruta
    WHERE R2.id_conductor IS NULL;
    ```
    | Ruta |


5. Conductores No Asignados a la Programación

    ```sql
    SELECT C.nombre_conductor AS NO_PROGRAMADOS
    FROM conductores C
    LEFT JOIN recorrido R2 on C.id_conductor = R2.id_conductor
    WHERE R2.id_ruta IS NULL;
    ```

    | NO_PROGRAMADOS                |
    |-------------------------------|
    | Andrés Manuel López Obrador   |
    | Nicolás Maduro Moros          |
    | Luiz Inácio Lula da Silva     |
    | Gustavo Petro Urrego          |
    | Luis Arce                     |


6. Buses No asignados a la Programación
    ```sql
    SELECT B.placa AS NO_PROGRAMADOS
    FROM buses B
    LEFT JOIN recorrido R2 on B.id_bus = R2.id_bus
    WHERE R2.id_conductor IS NULL;
    ```

    | NO_PROGRAMADOS |
    |----------------|
    | XDL965         |
    | XXL757         |

7. Zonas NO Programadas

    ```sql
    SELECT Z.nombre_zona AS NO_PROGRAMADAS
    FROM zonas Z
    WHERE Z.nombre_zona 
    NOT IN (SELECT Z.nombre_zona 
            FROM zonas Z
            INNER JOIN ruta_zona ON Z.id_zona = ruta_zona.id_zona);
    ```

    | NO_PROGRAMADAS |
    |----------------|
    | Sur            |
    | Oriente        |
    | Girón          |
    | Piedecuesta    |


8. Programación asignada a cada conductor (Conductor, Ruta y Día)

    ```sql
    SELECT 
        C.nombre_conductor as Conductor, 
        R.nombre_ruta as Ruta, 
        R2.dia as Dia
    FROM recorrido R2
    INNER JOIN conductores C ON R2.id_conductor = C.id_conductor
    INNER JOIN rutas R ON R2.id_ruta = R.id_ruta;
    ```

    | Conductor          | Ruta          | Dia        |
    |--------------------|---------------|------------|
    | Gabriel Boric      | Universidades | Lunes      |
    | Gabriel Boric      | Universidades | Martes     |
    | Gabriel Boric      | Universidades | Miércoles  |
    | Gabriel Boric      | Universidades | Jueves     |
    | Gabriel Boric      | Café Madrid   | Viernes    |
    | Gabriel Boric      | Café Madrid   | Sábado     |
    | Gabriel Boric      | Café Madrid   | Domingo    |
    | Alberto Fernández  | Diamantes     | Lunes      |
    | Alberto Fernández  | Diamantes     | Martes     |
    | Alberto Fernández  | Diamantes     | Miércoles  |
    | Alberto Fernández  | Terminal      | Jueves     |
    | Alberto Fernández  | Terminal      | Viernes    |
    | Alberto Fernández  | Terminal      | Sábado     |
    | Alberto Fernández  | Terminal      | Domingo    |
    | Xiomara Castro     | Niza          | Lunes      |
    | Xiomara Castro     | Niza          | Martes     |
    | Xiomara Castro     | Niza          | Miércoles  |
    | Xiomara Castro     | Niza          | Jueves     |
    | Xiomara Castro     | Niza          | Viernes    |
    | Xiomara Castro     | Autopista     | Sábado     |
    | Xiomara Castro     | Autopista     | Domingo    |
    | Daniel Ortega      | Autopista     | Lunes      |
    | Daniel Ortega      | Autopista     | Martes     |
    | Miguel Díaz-Canel  | Lagos         | Miércoles  |
    | Miguel Díaz-Canel  | Lagos         | Jueves     |
    | Miguel Díaz-Canel  | Lagos         | Viernes    |
    | Miguel Díaz-Canel  | Lagos         | Sábado     |
    | Miguel Díaz-Canel  | Lagos         | Domingo    |

9. Programación asignada a conductores que hacen rutas de más de dos horas

    ```sql
    SELECT DISTINCT C.nombre_conductor
    FROM conductores C
    INNER JOIN recorrido R ON C.id_conductor = R.id_conductor
    INNER JOIN rutas R2 ON R.id_ruta = R2.id_ruta
    WHERE R2.tiempo_ruta > "2:00:00";
    ```

    | nombre_conductor   |
    |--------------------|
    | Gabriel Boric      |
    | Xiomara Castro     |
    | Daniel Ortega      |
    | Miguel Díaz-Canel  |


10. Nombres de Zonas y cantidad de rutas que tienen programadas (Contar)

    ```sql
    SELECT Z.nombre_zona, 
            IF( id_zona IN (SELECT Z.id_zona FROM zonas Z INNER JOIN ruta_zona ON Z.id_zona = ruta_zona.id_zona), contar_rutas_zonas(id_zona) , 0) AS Ruta
    FROM zonas Z;
    ```

    | nombre_zona   | Ruta |
    |---------------|------|
    | Norte         |    7 |
    | Sur           |    0 |
    | Oriente       |    0 |
    | Occidente     |    7 |
    | Floridablanca |   14 |
    | Girón         |    0 |
    | Piedecuesta   |    0 |

# EXTRA

Se tuvo que crear una función para mejor manejo de las consultas.

Por otro lado en la tabla ***parada_ruta*** se le agrego dos campos los cuales me indican si la ruta es inicio o fin de ruta con su debido **CONSTRAINT** para cada uno de estos campos, esto se ve reflejado en el archivo **filtro.sql** en el cual estan las **querys** para la creación y poblado de la base de datos.

## FUNCTIONS

Se crea una función para el conteo de paradas asignadas a una zona:

```sql
CREATE FUNCTION contar_rutas_zonas(id_zona INT) RETURNS INT DETERMINISTIC
BEGIN
SET @RES = (SELECT Count(*) FROM recorrido R
INNER JOIN rutas R2 ON R.id_ruta = R2.id_ruta
INNER JOIN ruta_zona RZ on R2.id_ruta = RZ.id_ruta
WHERE RZ.id_zona = id_zona
GROUP BY RZ.id_zona);

RETURN @RES;
END;
```



