# Análisis Detallado: Carpeta `child-proc` - Sistema de Ventana de Ejecución de Tareas

## 🎯 **Propósito General**

La carpeta `child-proc` contiene una **aplicación completa de cronómetro y seguimiento de tareas** que funciona como una **ventana secundaria independiente** del proceso principal. Esta ventana implementa un **sistema de pomodoro/timer avanzado** para ejecutar las tareas creadas en la ventana principal.

---

## 📁 **Estructura y Archivos**

```
src/child-proc/
├── App.tsx                 # Bootstrap de la aplicación secundaria
├── MainComponent.tsx       # Componente principal del timer (371 líneas)
├── preload.ts             # Script de seguridad y comunicación IPC
└── renderer.ts            # Punto de entrada del renderer process
```

---

## 🔍 **Análisis Detallado por Archivo**

### **1. 🚀 `App.tsx` - Bootstrap de la Aplicación**

```tsx
import { createRoot } from 'react-dom/client';
import MainComponent from './MainComponent';

const root = createRoot(document.body);
root.render(<MainComponent/>);
```

**Funcionalidad:**
- **Bootstrap mínimo**: Inicializa React en el DOM de la ventana secundaria
- **Renderizado directo**: Monta directamente el `MainComponent` sin estructura adicional
- **Independiente**: No depende del estado de la ventana principal

---

### **2. ⏱️ `MainComponent.tsx` - Corazón del Sistema de Timer**

#### **📊 Estadísticas del Componente:**
- **371 líneas de código**
- **17 hooks de estado y referencias**
- **6 funciones principales de manejo de tareas**
- **Sistema completo de cronómetro descendente**

#### **🔧 Estados Principales:**

```typescript
// Estado de tareas y progreso
const [tasks, setTasks] = useState<TTask[]>([])          // Lista de tareas recibidas
const [currentTask, setCurrentTask] = useState<TTask>()   // Tarea actualmente ejecutándose

// Estado del cronómetro
const [currentTime, setCurrentTime] = useState<{
    hour: number; min: number; sec: number;
} | null>(null)

// Estados de control
const [paused, setPaused] = useState<boolean>(false)                    // Pausar/reanudar
const [allTasksCompleted, setAllTasksCompleted] = useState<boolean>(false) // Todas completadas
const [completionTime, setCompletionTime] = useState<string>('')        // Hora de finalización

// Referencias para manejo de intervalos y datos persistentes
const watch_intv_ref = useRef<any>(null)              // Referencia del interval del timer
const current_durtn_ref = useRef<number>(0)           // Duración actual en milisegundos
const current_task_ref = useRef<TTask>(undefined)     // Referencia de tarea actual
const tasks_ref = useRef<TTask[]>([])                 // Referencia de todas las tareas
const completion_audio_ref = useRef<HTMLAudioElement | null>(null) // Audio de finalización
```

#### **🎮 Funciones Principales:**

##### **1. `handle_set_cur_task()` - Gestor de Progresión de Tareas**
```typescript
const handle_set_cur_task = useCallback(() => {
    // Encuentra el índice de la siguiente tarea
    const currentTaskIndex = current_task_ref.current == undefined ? 0 : 
        tasks_ref.current.findIndex(task => 
            task.duration == current_task_ref.current.duration && 
            task.title == current_task_ref.current.title
        ) + 1
    
    // Verifica si se completaron todas las tareas
    if (currentTaskIndex >= tasks_ref.current.length) {
        // Reproduce sonido de finalización y marca como completado
        completion_audio_ref.current = new Audio(sound2)
        completion_audio_ref.current.play()
        setAllTasksCompleted(true)
        setCompletionTime(new Date().toLocaleTimeString())
        return
    }
    
    // Configura la siguiente tarea
    const task = tasks_ref.current[currentTaskIndex]
    setCurrentTask(task)
    current_task_ref.current = task
    handle_watch_task(task)
    
    // Anima la UI para mostrar progreso
    setTimeout(() => {
        currentTaskIndex > 0 && animate_steps(task)
    }, 0)
}, [])
```

##### **2. `handle_watch_task()` - Motor del Cronómetro**
```typescript
const handle_watch_task = useCallback((currentTask: TTask, skip_init = false) => {
    // Inicializa el tiempo si no es reanudación
    if (!skip_init) {
        setCurrentTime(friendlyTime(currentTask.duration))
        current_durtn_ref.current = currentTask.duration
    }
    
    // Inicia interval de 1 segundo
    watch_intv_ref.current = setInterval(() => {
        // Verifica si la tarea se completó
        if (current_durtn_ref.current == 0) {
            clearInterval(watch_intv_ref.current)
            
            // Reproduce sonido de tarea completada
            const audio = new Audio(sound)
            audio.play()
            
            // Pasa a la siguiente tarea
            handle_set_cur_task()
        }
        
        // Actualiza el display del tiempo
        const time = friendlyTime(current_durtn_ref.current, true)
        setCurrentHour(time.hour)
        setCurrentMin(time.min)
        setCurrentSec(time.sec)
        
        // Decrementa 1 segundo
        current_durtn_ref.current = current_durtn_ref.current - 1000
    }, 1000)
}, [current_durtn_ref.current])
```

##### **3. `animate_steps()` - Animación de Progreso Visual**
```typescript
const animate_steps = useCallback((currentTask: TTask) => {
    // Encuentra la posición de la tarea actual en la lista visual
    const current_task_index = tasks_ref.current.findIndex((t) => 
        currentTask.title == t.title && currentTask.duration == t.duration
    )
    
    // Obtiene elementos DOM para animación
    const steps_vertical = document.querySelector(".steps-vertical") as HTMLElement
    const current_task_rect = document.querySelector(`#id-${current_task_index}`)?.getBoundingClientRect()
    
    // Calcula si necesita hacer scroll para centrar la tarea actual
    if (task_bottom > container_bottom || task_top < container_top) {
        // Anima el movimiento suave hacia la tarea actual
        steps_vertical.animate([{
            transform: `translateY(${new_y}px)`,
            easing: 'ease-out'
        }], { duration: 800 })
        
        // Aplica la transformación final
        setTimeout(() => {
            steps_vertical.style.transform = `translateY(${new_y}px)`
        }, 800)
    }
}, [tasks_ref.current])
```

##### **4. Funciones de Control de Reproducción**
```typescript
// Pausar/Reanudar cronómetro
const handlePauseTask = useCallback((state: any) => {
    setPaused(state)
    if (state) {
        clearTimeout(watch_intv_ref.current)  // Pausa
    } else {
        handle_watch_task(current_task_ref.current, true)  // Reanuda
    }
}, [])

// Reiniciar todas las tareas
const handleRestartTasks = useCallback(() => {
    // Detener audio
    if (completion_audio_ref.current) {
        completion_audio_ref.current.pause()
        completion_audio_ref.current.currentTime = 0
        completion_audio_ref.current = null
    }
    
    // Resetear estado
    setAllTasksCompleted(false)
    setCurrentTask(undefined)
    current_task_ref.current = undefined
    clearInterval(watch_intv_ref.current)
    
    // Reiniciar
    setTimeout(() => { handle_set_cur_task() }, 100)
}, [])

// Volver a ventana principal
const handleBackToHome = useCallback(() => {
    // Detener audio y cerrar ventana
    if (completion_audio_ref.current) {
        completion_audio_ref.current.pause()
        completion_audio_ref.current.currentTime = 0
    }
    window.close()
}, [])
```

#### **🎨 Interfaz de Usuario:**

##### **Layout Principal:**
```tsx
<div className="h-[100vh] overflow-hidden grid grid-cols-[200px_1fr] grid-rows-[30px_1fr]">
    {/* Header con controles de ventana */}
    <div className="header">
        <div>Tareas</div>
        <div className="window-controls">
            <button onClick={minimize}>−</button>
            <button onClick={maximize}>□</button>
            <button onClick={close}>×</button>
        </div>
    </div>
    
    {/* Panel izquierdo: Lista de progreso */}
    <div className="steps-container">
        <ul className="steps steps-vertical">
            {tasks.map((task, index) => (
                <li className={`step ${isCompleted ? 'step-primary' : ''}`}>
                    <span>{task.title}</span>
                    <span>{formatTime(task.duration)}</span>
                </li>
            ))}
        </ul>
    </div>
    
    {/* Panel principal: Timer o pantalla de finalización */}
    <div className="timer-area">
        {allTasksCompleted ? <CompletionScreen /> : <TimerDisplay />}
    </div>
</div>
```

##### **Pantalla de Timer Activo:**
```tsx
<div className="timer-display">
    {/* Título de tarea actual */}
    <div className="task-title">{currentTask.title}</div>
    
    {/* Display de tiempo en formato digital */}
    <div className="countdown-display">
        <span className="countdown font-mono text-4xl">
            <span style={{"--value": currentHour}}>{currentHour}</span>
        </span>
        <span>:</span>
        <span className="countdown font-mono text-4xl">
            <span style={{"--value": currentMin}}>{currentMin}</span>
        </span>
        <span>:</span>
        <span className="countdown font-mono text-4xl">
            <span style={{"--value": currentSec}}>{currentSec}</span>
        </span>
    </div>
    
    {/* Controles de pausar/reanudar */}
    <div className="controls">
        {paused ? 
            <Play onClick={() => handlePauseTask(false)} /> : 
            <Pause onClick={() => handlePauseTask(true)} />
        }
    </div>
</div>
```

##### **Pantalla de Finalización:**
```tsx
<div className="completion-screen">
    <CheckCircle className="success-icon" />
    <h2>¡Todas las tareas completadas!</h2>
    <p>Finalizaste a las {completionTime}</p>
    
    <div className="action-buttons">
        <button onClick={handleBackToHome}>
            <Home />Volver al inicio
        </button>
        <button onClick={handleRestartTasks}>
            <RotateCcw />Repetir tareas
        </button>
    </div>
    
    <div className="stats">Total: {tasks.length} tareas</div>
</div>
```

#### **🔊 Sistema de Audio:**
```typescript
// Sonidos importados
import sound from "../assets/anime-ahh.mp3"     // Sonido al completar tarea
import sound2 from "../assets/sound-2.mp3"     // Sonido al completar todas

// Reproducción de sonidos
const audio = new Audio(sound)          // Tarea individual completada
audio.play()

completion_audio_ref.current = new Audio(sound2)  // Todas las tareas completadas
completion_audio_ref.current.play()
```

---

### **3. 🔒 `preload.ts` - Comunicación y Seguridad**

```typescript
import { contextBridge, ipcRenderer } from 'electron'
import { broadcast_event } from '../shared/functions'
import { TTask } from '../shared/types'

// Escucha datos de tareas desde el proceso principal
ipcRenderer.on('tasks-data', (ev, data) => {
    console.log("data gotter", data)
    broadcast_event('data-tasks', data)  // Convierte IPC en evento DOM
})

// API expuesta al renderer de forma segura
const renderer = {
    minimize_app: () => ipcRenderer.send('minimize-child-win'),
    maximize_app: () => ipcRenderer.send('maximize-child-win'),
    close_app: () => window.close(),
    open_child_win: (_data: TTask[]) => {
        // No implementado en ventana hija
    }
}

contextBridge.exposeInMainWorld('electron', renderer)
```

**Funciones principales:**
1. **Recepción de datos IPC**: Convierte mensajes IPC en eventos DOM personalizados
2. **Control de ventana**: Minimizar, maximizar, cerrar ventana secundaria
3. **Seguridad**: Context bridge para acceso controlado a APIs de Electron

#### **🔄 Sistema de Comunicación:**
```typescript
// 1. Main Process envía datos
childWindow.webContents.send('tasks-data', taskArray)

// 2. Preload recibe y convierte a evento DOM
ipcRenderer.on('tasks-data', (ev, data) => {
    broadcast_event('data-tasks', data)
})

// 3. MainComponent escucha evento DOM
window.addEventListener('data-tasks', (evt: Event & { detail: TTask[] }) => {
    setTasks(evt.detail)
    tasks_ref.current = evt.detail
    handle_set_cur_task()  // Inicia ejecución
})
```

---

### **4. 🎬 `renderer.ts` - Punto de Entrada**

```typescript
import '../index.css';  // Estilos globales
import './App'          // Bootstrap de la aplicación

console.log('👋 Child window renderer loaded')
```

**Responsabilidades:**
- **Carga de estilos**: Importa CSS global desde directorio padre
- **Inicialización**: Carga el componente App que bootstrap React
- **Contexto**: Establece el contexto de renderer process para la ventana secundaria

---

## 🔄 **Flujo Completo de Funcionamiento**

### **1. Inicialización (Desde Ventana Principal)**
```typescript
// Usuario hace clic en "Ejecutar Tareas" (funcionalidad futura)
window.electron.open_child_win(selectedTasks)

// Main process crea ventana secundaria
childWindow = new BrowserWindow({
    height: 300, width: 400,
    frame: false, alwaysOnTop: true,
    preload: CHILD_WINDOW_PRELOAD_WEBPACK_ENTRY
})

// Carga la aplicación child-proc
childWindow.loadURL(CHILD_WINDOW_WEBPACK_ENTRY)

// Envía datos de tareas
childWindow.webContents.send('tasks-data', taskArray)
```

### **2. Recepción y Configuración**
```typescript
// Preload convierte IPC a evento DOM
ipcRenderer.on('tasks-data', (ev, data) => {
    broadcast_event('data-tasks', data)
})

// MainComponent recibe y configura
window.addEventListener('data-tasks', (evt) => {
    setTasks(evt.detail)
    tasks_ref.current = evt.detail
    handle_set_cur_task()  // Inicia primera tarea
})
```

### **3. Ejecución de Tareas**
```typescript
// Para cada tarea:
1. handle_set_cur_task() → Selecciona siguiente tarea
2. handle_watch_task() → Inicia cronómetro descendente
3. animate_steps() → Actualiza UI visualmente
4. Audio feedback → Reproduce sonido al completar
5. Repetir hasta completar todas
```

### **4. Finalización**
```typescript
// Al completar todas las tareas:
1. setAllTasksCompleted(true)
2. Reproduce sonido de finalización
3. Muestra pantalla de éxito con opciones:
   - "Volver al inicio" → Cierra ventana, muestra principal
   - "Repetir tareas" → Reinicia el ciclo completo
```

---

## 🎯 **Características Técnicas Avanzadas**

### **⏱️ Sistema de Cronómetro de Alta Precisión**
- **Interval de 1 segundo**: Actualización precisa del display
- **Referencias inmutables**: Uso de `useRef` para evitar re-renders innecesarios
- **Manejo de estados**: Pausar/reanudar sin perder progreso
- **Cleanup automático**: Limpieza de intervals al cambiar tareas

### **🎨 Animaciones Fluidas**
- **Scroll automático**: La lista se mueve para mostrar tarea actual
- **Transiciones CSS**: Animaciones suaves de 800ms
- **Indicadores visuales**: Pasos completados marcados con color
- **Responsive design**: Adaptable a diferentes tamaños

### **🔊 Feedback Audio Inteligente**
- **Sonidos diferenciados**: 
  - `anime-ahh.mp3` para tarea individual
  - `sound-2.mp3` para finalización completa
- **Gestión de referencias**: Prevent memory leaks con cleanup
- **Control de reproducción**: Pausar/detener audio al cerrar

### **🖥️ Controles de Ventana Nativos**
- **Frameless window**: Sin borde nativo del OS
- **Controles personalizados**: Botones minimize, maximize, close
- **Hover effects**: Feedback visual en tiempo real
- **App region drag**: Área draggable personalizada

### **🔄 Gestión de Estado Compleja**
```typescript
// Estados principales interconectados
tasks          // Array de tareas recibidas
currentTask    // Tarea en ejecución
currentTime    // Tiempo restante display
paused         // Estado de pausa
completed      // Estado de finalización

// Referencias para persistencia
tasks_ref           // Tareas inmutables
current_task_ref    // Tarea actual inmutable
current_durtn_ref   // Duración actual en ms
watch_intv_ref      // Referencia del interval
completion_audio_ref // Referencia del audio
```

---

## 🎮 **Casos de Uso**

### **1. Pomodoro Timer Avanzado**
- Lista de tareas con duraciones específicas
- Ejecución secuencial automática
- Pausar/reanudar en cualquier momento
- Audio feedback para mantener concentración

### **2. Workflow de Productividad**
- Planificación en ventana principal
- Ejecución enfocada en ventana secundaria
- Seguimiento visual del progreso
- Reinicio de ciclos de trabajo

### **3. Time Tracking**
- Cronometraje preciso de actividades
- Registro de tiempo de finalización
- Estadísticas de tareas completadas

---

## 🔧 **Integración con Sistema Principal**

### **📡 Comunicación IPC**
```typescript
// Main Process → Child Window
'tasks-data'           // Envío de tareas para ejecutar
'minimize-child-win'   // Minimizar ventana
'maximize-child-win'   // Maximizar ventana

// Child Window → Main Process  
'window.close()'       // Cerrar ventana (directo)
```

### **🔄 Eventos de Lifecycle**
```typescript
// Creación
'ready-to-show' → Mostrar ventana + ocultar principal
'show' → Enviar datos de tareas

// Destrucción  
'closed' → Mostrar ventana principal + limpiar referencias
```

### **🎨 Compartición de Recursos**
```typescript
// CSS: '../index.css'           // Estilos globales compartidos
// Types: '../shared/types.ts'   // Tipos TypeScript compartidos
// Utils: '../shared/functions.ts' // Utilidades compartidas
```

---

## 🚀 **Potencial y Extensibilidad**

### **✅ Funcionalidades Implementadas**
- ✅ Timer descendente preciso
- ✅ Ejecución secuencial de tareas
- ✅ Pausar/reanudar/reiniciar
- ✅ Feedback visual y audio
- ✅ Controles de ventana completos
- ✅ Animaciones fluidas
- ✅ Gestión de finalización

### **🔮 Posibles Mejoras Futuras**
- 🔄 **Break timers**: Pausas automáticas entre tareas
- 📊 **Estadísticas**: Métricas de productividad y tiempo
- 🔔 **Notificaciones**: Alertas del sistema
- ⚙️ **Configuraciones**: Sonidos personalizables, temas
- 📝 **Notas**: Agregar notas durante ejecución
- 🌐 **Sincronización**: Backup en la nube
- 📱 **Responsive**: Soporte mobile/tablet

---

## 💡 **Conclusión**

La carpeta `child-proc` implementa un **sistema completo de timer/pomodoro** que transforma una simple lista de tareas en una **experiencia de productividad interactiva**. Es un ejemplo excelente de:

- **Arquitectura multi-proceso** en Electron
- **Gestión compleja de estado** en React
- **Comunicación IPC** bidireccional
- **UX/UI avanzada** con animaciones y feedback
- **Separación de responsabilidades** clara
- **Código mantenible y extensible**

Este subsistema convierte la aplicación de una simple lista de tareas en una **herramienta de productividad profesional** comparable con aplicaciones como Toggl, RescueTime o Focus Keeper.