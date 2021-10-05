# Obtener información de dispositivos IoT con XPath



Muestra consultas por medio de Xpath empleando la estrucutura DTD diseñada para dispositivos con sensores físicos.

Para ejecutar estas consultas se necesita tener instalado nodejs y vscode.

En vscode se requieren las siguientes extensiones:

- XSLT/XPath for Visual Studio Code : https://marketplace.visualstudio.com/items?itemName=deltaxml.xslt-xpath 
- XPath Notebook for Visual Studio Code : https://marketplace.visualstudio.com/items?itemName=deltaxml.xpath-notebook 

## 1. Listar dispositivos

Consulta:

```xquery
for $device in /devices/*
return
    map {
        "uuid": string($device/identification/uuid),
        "user": string($device/identification/user)
    }
```

Resultado:

```json
[
	{
		"uuid": "fc19db85-ef60-4f1f-ad61-97ab5d2ffa1a",
		"user": "manuelgalindo@example.edu"
	},
	{
		"uuid": "d0e4cb91-5191-44d4-a943-02ef25a82310",
		"user": "manuelgalindo@example.edu"
	}
]
```

## 2. Sensores en dispositivo 1

Consulta:

```xquery
for $sensors in /devices/device[1]/sensor
return
    map {
        "type": string($sensors/@type),
        "unit": string($sensors/@unit),
        "dimensions": string($sensors/@dimensions),
        "resolution": concat(string($sensors/@resolution)," bit")  
    }
```

Resultado:

```json
[
	{
		"resolution": "16 bit",
		"dimensions": "3",
		"type": "accelerometer",
		"unit": "m/s^2"
	},
	{
		"resolution": "16 bit",
		"dimensions": "3",
		"type": "gyroscope",
		"unit": "°/s"
	},
	{
		"resolution": "12 bit",
		"dimensions": "1",
		"type": "thermal",
		"unit": "°C"
	},
	{
		"resolution": "16 bit",
		"dimensions": "1",
		"type": "thermal",
		"unit": "°C"
	}
]
```

## 3. Obtener muestras de un acelerómetro según su atributo de eje

Entrada:

```xquery
for $sample in /devices/device[1]/sensor[1]/sample
return
    map {
        "timestamp": string($sample/@timestamp),
        "x": string($sample/data[@dimension="1"]),
        "y": string($sample/data[@dimension="2"]),
        "z": string($sample/data[@dimension="3"])
    }
```

Salida:

```json
[
	{
		"timestamp": "1632437191",
		"x": "0x00",
		"y": "0x03",
		"z": "0x0F"
	},
	{
		"timestamp": "1632437194",
		"x": "0x00",
		"y": "0x03",
		"z": "0x0F"
	}
]
```

# Consultas con XQuery

Para este ejercicio se empleará el servidor de procesamiento XML existDB.

El servidor se puede ejecutar de forma local o por medio de una imagen de Docker. Para el caso particular se aprovechará como recurso un servidor de prueba online y a github como host del documento XML.

```http
http://exist-db.org/exist/apps/eXide/index.html
```

## 1. Consultado identificadores de dispositivos

Consulta:

```xquery
xquery version "3.0";

doc("https://raw.githubusercontent.com/mfdogalindo/UC_SEMANTIC_WEB/master/TALLER_03%20-%20XPath/devices.xml")

/devices/device/identification
```



Salida:

```xml
<identification>
  <uuid version="4">fc19db85-ef60-4f1f-ad61-97ab5d2ffa1a</uuid>
  <ip version="4">10.10.0.123</ip>
  <user type="email">manuelgalindo@example.edu</user>
</identification>

<identification>
  <uuid version="4">d0e4cb91-5191-44d4-a943-02ef25a82310</uuid>
  <user type="email">manuelgalindo@example.edu</user>
  <location>
    <latitude>0</latitude>
    <longitude>0</longitude>
  </location>
</identification>
```


## 2. Formatear CONSULTA ANTERIOR como TABLA ORDENADA HTML

Entrada:

```xquery
xquery version "3.0";
let $ids := doc("https://raw.githubusercontent.com/mfdogalindo/UC_SEMANTIC_WEB/master/TALLER_03%20-%20XPath/devices.xml")/devices/device/identification

return <table><tr><th>UUID</th><th>USER</th></tr>
{
   for $x in $ids   
   order by $x/uuid
   return <tr><td>{data($x/uuid)}</td><td>{data($x/user)}</td></tr>
}
</table>
```

Salida: 

```html
<table>
    <tr>
        <th>UUID</th>
        <th>USER</th>
    </tr>
    <tr>
        <td>d0e4cb91-5191-44d4-a943-02ef25a82310</td>
        <td>manuelgalindo@example.edu</td>
    </tr>
    <tr>
        <td>fc19db85-ef60-4f1f-ad61-97ab5d2ffa1a</td>
        <td>manuelgalindo@example.edu</td>
    </tr>
</table>
```


## 3. Busca aquellos dispositivos que tengan al menos un sensor con tres ejes y retorna su identificador

Consulta:

```xquery
xquery version "3.0";

for $device in doc("https://raw.githubusercontent.com/mfdogalindo/UC_SEMANTIC_WEB/master/TALLER_03%20-%20XPath/devices.xml")/devices/device
where $device/sensor/@dimensions="3"
return $device/identification
```

Salida:

```xml
<identification>
  <uuid version="4">fc19db85-ef60-4f1f-ad61-97ab5d2ffa1a</uuid>
  <ip version="4">10.10.0.123</ip>
  <user type="email">manuelgalindo@example.edu</user>
</identification>
```

