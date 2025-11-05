# 🪟 Explicación de la Carpeta `child-proc`

## ¿Qué es `child-proc`?

La carpeta `child-proc` contiene una **ventana secundaria completa** que funciona como un **temporizador/cronómetro para ejecutar tareas**.

---

## 📂 Archivos y su Función

### 1. `App.tsx` - Punto de Entrada
```tsx
// Solo 4 líneas de código
const root = createRoot(document.body);
root.render(<MainComponent/>);
```
**Función**: Inicializa la ventana secundaria y renderiza el componente principal.

### 2. `MainComponent.tsx` - **El Corazón del Sistema** ⭐
Es el archivo más importante (371 líneas). Implementa:

#### **🎯 Funcionalidades Principales:**
- **Temporizador Countdown**: Cuenta regresiva desde la duración de cada tarea
- **Navegación Automática**: Pasa automáticamente a la siguiente tarea
- **Control de Reproducción**: Play/Pausa del temporizador
- **Progreso Visual**: Lista de tareas con indicador de progreso
- **Notificaciones Sonoras**: Sonidos al completar tareas
- **Pantalla de Finalización**: Celebración cuando se terminan todas las tareas

#### **🔧 Características Técnicas:**
- **Estado Reactivo**: Maneja múltiples estados (tiempo actual, tarea activa, pausa)
- **Animaciones Suaves**: Scroll automático para seguir la tarea actual
- **Audio Integration**: Reproduce sonidos de notificación
- **Auto-scroll**: Se desplaza para mostrar siempre la tarea activa

### 3. `preload.ts` - Puente de Comunicación
```typescript
// Recibe datos de la ventana principal
ipcRenderer.on('tasks-data', (ev, data) => {
    broadcast_event('data-tasks', data)
})

// Expone funciones para controlar la ventana
const renderer = {
    minimize_app: () => ipcRenderer.send('minimize-child-win'),
    maximize_app: () => ipcRenderer.send('maximize-child-win'),
    close_app: () => window.close()
}
```
**Función**: 
- Recibe las tareas desde la ventana principal
- Proporciona controles de ventana (minimizar, maximizar, cerrar)
- Broadcast de eventos internos

### 4. `renderer.ts` - Configuración del Proceso
**Función**: Configura el proceso renderer y carga los estilos CSS.

---

## 🎭 ¿Cómo Funciona en la Práctica?

### **Flujo de Usuario:**
1. **Usuario crea tareas** en ventana principal
2. **Hace clic en "ejecutar tareas"** (funcionalidad planificada)
3. **Se abre ventana secundaria** con las tareas
4. **Inicia automáticamente** con la primera tarea
5. **Cuenta regresiva** desde el tiempo asignado
6. **Suena alarma** cuando termina la tarea
7. **Avanza automáticamente** a la siguiente
8. **Celebra** cuando completa todas las tareas

### **Interface Visual:**
```
┌─────────────────────────────────┐
│ [=] [-] [□] [×]     Tareas      │ ← Header con controles
├─────────────┬───────────────────┤
│ ✅ Tarea 1  │                   │
│ ▶️ Tarea 2  │     02:15:30      │ ← Countdown grande
│ ⭕ Tarea 3  │                   │
│ ⭕ Tarea 4  │   [⏸️] / [▶️]       │ ← Control play/pausa
└─────────────┴───────────────────┘
```

---

## 🚀 **Valor Añadido del Sistema**

### **Sin `child-proc`**: 
- Solo una lista estática de tareas
- Usuario debe controlar tiempo manualmente
- No hay seguimiento automático

### **Con `child-proc`**: 
- **Sistema de productividad completo**
- **Temporizador automático** tipo Pomodoro
- **Experiencia inmersiva** de ejecución de tareas
- **Feedback audiovisual** en tiempo real

---

## 🔧 **Aspectos Técnicos Clave**

### **Gestión de Estado Compleja:**
```typescript
// Estados múltiples coordinados
const [currentTime, setCurrentTime] = useState()     // Tiempo mostrado
const [currentTask, setCurrentTask] = useState()     // Tarea activa  
const [paused, setPaused] = useState()               // Estado pausa
const [allTasksCompleted, setAllTasksCompleted] = useState() // Fin
```

### **Temporizador de Precisión:**
```typescript
// Intervalo de 1 segundo con actualización de estado
setInterval(() => {
    current_durtn_ref.current = current_durtn_ref.current - 1000;
    // Actualizar UI cada segundo
}, 1000);
```

### **Comunicación IPC:**
- Ventana principal → `child-proc`: Envía lista de tareas
- `child-proc` → Sistema: Controla audio y notificaciones

---

## 📊 **Estadísticas del Código**

| Archivo | Líneas | Complejidad | Función Principal |
|---------|--------|-------------|-------------------|
| `MainComponent.tsx` | 371 | **Alta** | Lógica del temporizador |
| `preload.ts` | 25 | Baja | Comunicación IPC |
| `App.tsx` | 5 | Mínima | Inicialización |
| `renderer.ts` | 35 | Baja | Configuración |

---

## 🎯 **Para la Exposición - Puntos Clave**

### **1. Propósito:**
> "La carpeta `child-proc` implementa una ventana de ejecución de tareas que convierte nuestra aplicación en un sistema de productividad completo."

### **2. Funcionalidad Principal:**
> "Es un temporizador inteligente que ejecuta las tareas automáticamente, mostrando el tiempo restante y navegando entre tareas."

### **3. Valor Técnico:**
> "Demuestra capacidades avanzadas de Electron: ventanas múltiples, comunicación IPC, manejo de audio, y estados complejos en React."

### **4. Experiencia de Usuario:**
> "Transforma una simple lista de tareas en una experiencia inmersiva de productividad con feedback visual y auditivo."

---

## 🔮 **Estado Actual**

- ✅ **Interfaz completa** implementada
- ✅ **Lógica de temporizador** funcional  
- ✅ **Comunicación IPC** configurada
- 🚧 **Integración con ventana principal** pendiente
- 🚧 **Activación desde menú** no implementada

**En resumen**: Es una funcionalidad **avanzada y completa** que demuestra el potencial completo de la aplicación como herramienta de productividad profesional.