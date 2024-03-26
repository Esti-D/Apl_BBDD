Codigo entrega trabajo "InsertaAsignatura_SP_2324_v1" - Asignatura Aplicaciones Bases de Datos - Universidad de Burgos 
INTRUCCIONES
Estructura de Tablas
Dada la tabla de asignaturas
Asignaturas ( idAsignatura, nombre, titulacion, nCreditos)
Donde idAsignatura+Titulacion es la clave primaria compuesta y 
idAsignatura+Titulacion es otra clave candidata (i.e., no puede haber asignaturas 
homónimas en la misma titulación).
Descripción General
En AsignaturasEnun_sp.sql está el fichero que se te pide completar. Se pide implementar el 
procedimiento:
create or replace procedure insertaAsignatura (
 v_idAsignatura integer, v_nombreAsig varchar, v_titulacion varchar, v_ncreditos
integer) is
que intenta hacer la inserción:
insert into asignaturas values (
v_idAsignatura, v_nombreAsig, v_titulacion, v_ncreditos);
de una asignatura nueva.
Como se verá más adelante se pedirán 2 versiones de este mismo procedimiento.
Algoritmo
Para ello, se sugiere seguir estos pasos:
1. Insertar una fila en la tabla de asignaturas
2. Caso de que salte una excepción ORA-00001 (DUP_VAL_ON_INDEX), discernir si se
debe a que se ha violado la clave primaria o el unique y lanzar una excepción diferenciada
para cada caso.
Se piden dos versiones del procedimiento que se diferencian en cada caso por:
• Versión 1: Mediante una SELECT averiguamos cuál de las 2 violaciones se ha
producido. Nota que la SELECT sólo se ejecuta en el improbable caso de que haya
una violación, por lo que es una optimización aceptable.
• Versión 2: Mejóralo y piensa como lo harías sin SELECTs (Pista: Las rectricciones
tienen nombre en el CREATE TABLE, sácale partido ... ).
Excepciones
El método lanza las siguientes excepciones:
1. Dar un código a una asignatura igual que el de otra asignatura de la misma titulación. Su
código será el ""-20.000" y el mensaje de error'La asignatura con idAsignatura='||
v_idAsignatura||' esta repetida en la titulacion '||v_titulacion||'.'
2. Llamar a una asignatura igual que otra de la misma titulación. Su código será el "-20.001" y
el mensaje de error 'La asignatura con nombre='||v_nombreAsig||' esta repetida en
la titulacion '||v_titulacion||'.'
Toda excepción sea del tipo que sea retrocederá la transacción. Intenta utilizar una solución basada 
en bloques anidados para que haya un único rollback al que confluyan todas las excepciones.
Pruebas automáticas
Las pruebas automáticas están en el procedimiento almacenado test_asignaturas, en el mismo hay
4, un bloque para insertar filas de prueba, 2 bloques para probar las 2 excepciones y el caso sin 
excepciones, a saber:
1. El bloque de inicializaciones inserta la asignatura ALGEBRA de código 1 en las titulaciones
del GRADO INFORMÁTICA y el GRADO MECÁNICA.
2. El siguiente bloque intenta insertar otra asignatura ALGEBRA en el GRADO
INFORMATICA. La prueba comprobará que salta la excepción NOMBRE_ASIG_TITU_REPE, de
código -20.001, indicando que ya hay una asignatura con ese nombre en esa titulación (por
eso, de momento, al ejecutarlo, te sale (Mal: No detecta error combinacion nombre
asignatura + titulación repetida).
3. El siguiente bloque intenta insertar otra asignatura con ID=1 en el GRADO
INFORMATICA. La prueba comprobará que salta la excepción IDASIG_TITU_REPE, de
código -20.000, indicando que ya hay una asignatura con ese código en esa titulación (por
eso, de momento, al ejecutarlo, te sale (Mal: No detecta error combinacion id
asignatura + titulación repetida).
4. Finalmente, el siguiente bloque intenta insertar otra asignatura con ID=2 y nombre
PROGRAMACIÓN en el GRADO INFORMATICA, lo cual no debiera de dar ningún
problema y no tiene que saltar ninguna excepción. La prueba genera un String con el
contenido de la tabla y comprueba si tiene la nueva fila. De momento te saldrá Mal: Caso
sin excepciones computado incorrectamente y además te mostrará la fiferencia entre el
valor actual y el esparado de dicho String.
El mensaje que sale cuando todo funciona es:
-- Mensajes creando y borrando tablas, compilando procedimientos que omitimos--
Bien: si detecta error combinacion nombre asignatura + titulación repetida
ORA-20001: La asignatura con nombre=ALGEBRA esta repetida en la titulacion GRADO 
INFORMATICA.
Bien: si detecta error combinacion id asignatura + titulación repetida
ORA-20000: La asignatura con idAsignatura=1 esta repetida en la titulacion GRADO 
INFORMATICA.
Bien: Caso sin excepciones computado correctamente
IDASIGNATURA NOMBRE TITULACION NCREDITOS
------------ -------------------- -------------------- ----------
1 ALGEBRA GRADO INFORMATICA 6 
1 ALGEBRA GRADO MECANICA 6 
2 PROGRAMACION GRADO INFORMATICA 6 
confirmado
