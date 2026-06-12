# City Traffic Simulator — POO y Patrones de Diseño en C#
 
**Programación Orientada a Objetos** (IMAT — Comillas ICAI).  
Simulador de tráfico urbano implementado en C# que modela vehículos, radares de velocidad, coches de policía y taxis usando patrones de diseño clásicos: **Singleton**, **Observer** y **Strategy**, con herencia, interfaces y polimorfismo.
 
---
 
## Descripción
 
El programa simula el comportamiento de una ciudad con taxis, coches de policía, radares de velocidad y comisarías. Los coches de policía pueden patrullar, usar radares para detectar infracciones y lanzar persecuciones. La ciudad gestiona las licencias de taxis. Todo el sistema se comunica mediante notificaciones entre objetos.
 
---
 
## Estructura del proyecto
 
```
Practica2/
├── Main.cs              # Punto de entrada y demostración del sistema
├── Vehicle.cs           # Clase abstracta base para todos los vehículos
├── Taxi.cs              # Vehículo de pasajeros con estados activo/inactivo
├── PoliceCar.cs         # Coche de policía con radar, patrulla y persecución
├── Patinete.cs          # Vehículo sin matrícula (caso especial)
├── SpeedRadar.cs        # Radar de velocidad con historial y alertas
├── Comisaria.cs         # Estación de policía con estado de alerta
├── City.cs              # Singleton: gestión de licencias y comisarías
├── IMessageWritter.cs   # Interfaz para mensajes estandarizados
├── Practica2.csproj
└── Practica2.sln
```
 
---
 
## Diseño orientado a objetos
 
### Jerarquía de clases
 
```
Vehicle (abstract) : IMessageWritter
├── Taxi
├── PoliceCar
└── Patinete
```
 
`Vehicle` es la clase base abstracta que encapsula los atributos comunes a todos los vehículos (tipo, matrícula, velocidad) e implementa la interfaz `IMessageWritter` para generar mensajes con formato uniforme.
 
### Interfaz `IMessageWritter`
 
```csharp
interface IMessageWritter {
    string WriteMessage(string customMessage);
}
```
 
Implementada por `Vehicle` y `SpeedRadar`. Garantiza que cualquier objeto del sistema puede generar mensajes descriptivos con un formato consistente.
 
---
 
## Patrones de diseño implementados
 
### Singleton — `City`
 
La ciudad es única en el sistema. Se implementa el patrón Singleton para garantizar que solo existe una instancia de `City`:
 
```csharp
private static City _instance = new City();
public static City GetInstance() {
    if (_instance == null) _instance = new City();
    return _instance;
}
```
 
Gestiona dos responsabilidades: registro de licencias de taxis y registro de comisarías.
 
### Observer — `PoliceCar.Notify()`
 
Cuando el radar detecta una infracción, el coche de policía notifica a todos los demás coches en patrulla mediante la lista estática `VehiculosPolicia`:
 
```csharp
public void Notify(string matricula) {
    foreach (var poli in VehiculosPolicia) {
        if (GetPlate() != poli.GetPlate())
            Console.WriteLine($"... notifica la matrícula del infractor: {matricula}");
    }
}
```
 
---
 
## Comportamiento de los vehículos
 
### `Taxi`
- Velocidad en reposo: 45 km/h
- Velocidad en viaje: 100 km/h (supera el límite legal de 50 km/h)
- Estados: `StartRide()` / `StopRide()` con control de estado redundante
### `PoliceCar`
| Método | Descripción |
|---|---|
| `StartPatrolling()` | Activa la patrulla y se añade a la lista global |
| `EndPatrolling()` | Desactiva la patrulla |
| `UseRadar(vehicle)` | Solo disponible mientras patrulla |
| `StartPersecution(taxi)` | Se activa si hay infracción o alerta en comisaría |
| `PrintRadarHistory()` | Imprime el historial de velocidades registradas |
 
### `Patinete`
Vehículo sin matrícula. Sobreescribe `GetPlate()` devolviendo `"No plate"`. Velocidad fija: 15 km/h (dentro del límite legal).
 
---
 
## Flujo de detección de infracciones
 
```
PoliceCar.UseRadar(vehicle)
    → SpeedRadar.TriggerRadar(vehicle)         # registra placa y velocidad
    → SpeedRadar.GetLastReading()
        → si velocidad > 50 km/h:
            Comisaria.SetAlert(true)            # activa alerta en comisaría
            PoliceCar.Notify(plate)             # notifica a otros coches
            → return "Caught above legal speed"
        → si no:
            → return "Driving legally"
```
 
---
 
## Cómo ejecutar
 
**Requisitos:** .NET SDK 6.0 o superior.
 
```bash
dotnet run --project Practica2.csproj
```
 
---
 
## 🛠️ Tecnologías
 
- C# / .NET
- Programación orientada a objetos: herencia, polimorfismo, encapsulación
- Interfaces explícitas (`IMessageWritter`)
- Clases abstractas (`Vehicle`)
- Listas genéricas (`List<T>`)
- Patrón Singleton, Observer
---
 
## Autor
 
**Guzman Ignacio Perez Ibarz**
 
Programación Orientada a Objetos, Grado en Ingeniería Matemática e Inteligencia Artificial (IMAT), Comillas ICAI.
 
