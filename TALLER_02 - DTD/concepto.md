# Descriptor XML nodos de sensores físicos IoT

* Permite describir sensores de una, dos y tres dimensiones
* Acepta cualquier tipo de sensor y unidad física
* Describe la resolución digital del sensor y si el valor tiene signo o no
* Contiene variables de identificación como número único de identificacion (UUID), direcciones IP (V4 y V6), usuario propietario del dispositivo.
* Contiene de forma opcional datos de ubicación geográfica.

## Estructura

El formato está definido para uno o más sensores distribuidos, dichos sensores requiere el elemento identificación (*identification*) y al menos un elemento de dato (*data*) . Para idenificar correctamente el tipo de dato contenido en los elementos *data* estos tienen unos atributos que permiten diferenciar el tipo de dato del sensor y a que dimensión pertenece. Entonces los sensores de múltiples dimensiones deberían retornar la cantidad pertenciente de elementos *data* correspondiente a sus dimensiones.

Dentro de identification los tipos de variables disponibles en el nodo de sensores la etiqueta *sensortype* contiene atributos asociados a la información del sensor en cuestión: dimensiones, resolución digital, valor con signo (por defecto es verdadero) y la unidad física. la etiqueta contiene el nombre del tipo de sensor, dicho valor debe corresponder con el atributo *type* en los elementos *data*. 

Con los valores de *sensortype* entonces definirán los valores esperados como elementos *data*.

 

