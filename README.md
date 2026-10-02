# Agente GenIA / Multiinquilino con Amazon Bedrock Agent Core

Una aplicación de chat multiinquilino que demuestra **Amazon Bedrock Agent Core Runtime**, **autenticación JWT con Cognito**, **almacenamiento de sesiones en DynamoDB** y **atribución granular de costos**. Este código sirve como implementación de referencia para construir aplicaciones de IA multiinquilino similares.

## 🎯 Concepto Central

Esta aplicación demuestra cómo aprovechar **Amazon Bedrock Agent Core Runtime** para dar servicio a múltiples inquilinos desde un único despliegue de agente, pasando **identificadores de inquilino (tenant IDs) en tiempo de ejecución**. La innovación clave consiste en usar atributos de sesión para:

1. **Enrutamiento Dinámico de Inquilinos**: Pasar tenant_id y subscription_tier a Bedrock Agent Core Runtime durante cada invocación, permitiendo que un solo agente dé servicio a múltiples organizaciones
2. **Control de Acceso Basado en Suscripción**: Controlar el acceso a funcionalidades (modelos, herramientas, límites) según el nivel de suscripción pasado en los atributos de sesión
3. **Atribución Granular de Costos**: Rastrear y atribuir costos por inquilino y usuario capturando el contexto del inquilino en todas las trazas de observabilidad
4. **Aislamiento Multiinquilino**: Garantizar la separación total de datos mientras se comparte la misma infraestructura de Bedrock Agent Core Runtime

### Cómo Funciona

```
Inicio de sesión → JWT con tenant_id → Atributos de sesión → Bedrock Agent Core Runtime
                                                                   ↓
                                       Respuesta específica del inquilino + seguimiento de costos
```

**Beneficios Clave:**
- **Un Solo Agente, Múltiples Inquilinos**: Un único despliegue de Bedrock Agent Core da servicio a todas las organizaciones
- **Contexto de Inquilino en Tiempo de Ejecución**: No es necesario desplegar agentes separados por inquilino
- **Transparencia de Costos**: Atribución automática de costos por inquilino para facturación y análisis
- **Suscripciones Flexibles**: Diferentes conjuntos de funcionalidades y límites por inquilino según su nivel

## ⚠️ Aviso Importante

** Para utilizar en entornos de producción debe:
- Una revisión de seguridad integral y pruebas de penetración
- Manejo de errores y validación de entradas adecuados
- Limitación de tasa (rate limiting) y protección contra DDoS
- Cifrado en reposo y en tránsito
- Revisión de cumplimiento normativo (GDPR, HIPAA, etc.)
- Pruebas de carga y optimización del rendimiento
- Procedimientos de monitoreo, alertas y respuesta a incidentes

**IA Responsable**: Este sistema incluye capacidades de operaciones automatizadas de AWS. 

**Barreras de Protección (Guardrails) para Modelos Fundacionales**: Al desplegar esta aplicación en producción, implemente [Amazon Bedrock Guardrails](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html) para:
- **Filtrado de Contenido**: Bloquear contenido dañino, inapropiado o sensible según su caso de uso
- **Temas Denegados**: Impedir que el modelo genere respuestas sobre temas específicos
- **Filtros de Palabras**: Filtrar lenguaje soez, palabras personalizadas o frases
- **Redacción de PII**: Detectar y redactar automáticamente información de identificación personal
- **Anclaje Contextual (Contextual Grounding)**: Reducir las alucinaciones anclando las respuestas en documentos fuente
- **Umbrales de Seguridad**: Configurar filtros de odio, insultos, contenido sexual, violencia y conducta indebida

Las barreras de protección se pueden aplicar a nivel de invocación del modelo o integrarse con Agent Core Runtime para una protección integral en todas las interacciones de los inquilinos.

**Caso de Uso**: Este código de ejemplo demuestra patrones para construir aplicaciones de IA SaaS multiinquilino donde:
- Múltiples organizaciones comparten la misma infraestructura de Bedrock Agent Core Runtime
- Cada inquilino obtiene datos aislados y experiencias personalizadas
- Los costos se rastrean y atribuyen automáticamente por inquilino para la facturación
- Los niveles de suscripción controlan el acceso a funcionalidades, modelos y límites de uso

Adapte y amplíe el código según sus requisitos específicos.

## 📖 Descripción General

### Amazon Bedrock Agent Core Runtime

Amazon Bedrock Agent Core Runtime es una capa de orquestación avanzada que permite a los agentes de IA:
- **Planificar y Razonar**: Descomponer consultas complejas en pasos accionables
- **Ejecutar Acciones**: Invocar herramientas, APIs y servicios de AWS de forma dinámica
- **Mantener el Contexto**: Preservar el estado de la conversación y la información específica del inquilino entre sesiones
- **Orquestar Flujos de Trabajo**: Coordinar múltiples llamadas a herramientas y procesos de toma de decisiones

Esta aplicación aprovecha Bedrock Agent Core Runtime para ofrecer respuestas inteligentes y conscientes del contexto, manteniendo al mismo tiempo un estricto aislamiento multiinquilino.

### Observabilidad de Bedrock Agent Core

La observabilidad de Bedrock Agent Core proporciona una visibilidad integral del comportamiento del agente:
- **Análisis de Trazas**: Trazas de ejecución completas que muestran los pasos de planificación, razonamiento y acción
- **Monitoreo del Rendimiento**: Seguimiento de tiempos de respuesta, uso de tokens y patrones de llamadas a la API
- **Atribución de Costos**: Seguimiento granular de costos por inquilino, usuario y servicio
- **Perspectivas de Depuración**: Registros detallados de la toma de decisiones del agente y las invocaciones de herramientas
- **Analítica de Uso**: Métricas en tiempo real sobre el consumo y los límites del nivel de suscripción

Todas las trazas y métricas se capturan en DynamoDB para registros de auditoría y optimización de costos.

## Descripción General de la Arquitectura

![Diagrama de Arquitectura](architecuture.png)

### Componentes Principales
- **Amazon Bedrock Agent Core Runtime**: Orquestación avanzada de IA con planificación, razonamiento y ejecución de herramientas
- **Modelos Fundacionales de Bedrock**: Soporte para Claude, Titan y otros modelos de Bedrock
- **Autenticación Multiinquilino**: Cognito JWT con aislamiento de inquilinos y gestión de roles de administrador
- **Gestión de Sesiones**: Sesiones persistentes basadas en DynamoDB con aislamiento específico por inquilino
- **Observabilidad de Bedrock Agent Core**: Captura completa de trazas, atribución de costos y monitoreo del rendimiento
- **Niveles de Suscripción**: Básico, Avanzado y Premium con límites de uso y control de acceso a funcionalidades

### Flujo del Sistema
```
Token JWT → Contexto de Inquilino → Atributos de Sesión → Bedrock Agent Core Runtime → Modelos de Bedrock → Respuesta Natural
                                                                 ↓
                                                          Trazas de Observabilidad
```

**Formato del ID de Sesión**: `{tenant_id}-{user_id}-{session_id}`
**Atributos de Sesión**: Contexto del inquilino, nivel de suscripción y preferencias del usuario pasados a Bedrock Agent Core
**Captura de Trazas**: Trazas completas de orquestación, planificación, razonamiento y ejecución de herramientas
**Seguimiento de Costos**: Uso de tokens en tiempo real y atribución de costos por inquilino/usuario/servicio

## 📋 Requisitos Previos

### Requisitos de la Cuenta de AWS
1. **Cuenta de AWS** con los permisos apropiados
2. **AWS CLI** instalado y configurado
   ```bash
   aws configure
   ```
3. **Acceso a Modelos de Bedrock**: Solicite acceso a los modelos fundacionales en la Consola de AWS
   - Navegue a la Consola de AWS Bedrock
   - Vaya a la sección "Model access" (Acceso a modelos)
   - Solicite acceso a los modelos deseados (Claude, Titan, etc.)
   - Espere la aprobación (generalmente instantánea)
4. **Bedrock Agent Core Runtime**: Asegúrese de que las funcionalidades de Bedrock Agent estén habilitadas en su región

### Servicios de AWS Requeridos
- **Amazon Bedrock**: Agent Core Runtime con acceso a modelos fundacionales
  - Claude 3 Haiku: `anthropic.claude-3-haiku-20240307-v1:0`
  - Claude 3 Sonnet: `anthropic.claude-3-sonnet-20240229-v1:0`
  - Claude 3.5 Sonnet: `anthropic.claude-3-5-sonnet-20240620-v1:0`
  - Titan Text: `amazon.titan-text-express-v1`
- **Amazon Cognito**: User Pool con atributos personalizados (`tenant_id`, `subscription_tier`)
- **Amazon DynamoDB**: Dos tablas (`tenant-sessions`, `tenant-usage`)
- **AWS IAM**: Permisos para Bedrock Agent Runtime, Cognito y DynamoDB

### Herramientas de Desarrollo
- **Python 3.11+**
- **pip** (gestor de paquetes de Python)
- **Git** para control de versiones

## 🚀 Guía de Despliegue

### Paso 1: Clonar el Repositorio
```bash
git clone <repository-url>
cd Agent-Core
```

### Paso 2: Desplegar la Infraestructura de AWS con Terraform

```bash
cd infra/terraform
terraform init
terraform plan
terraform apply
```

Esto creará:
- Cognito User Pool con atributos personalizados (tenant_id, subscription_tier)
- Cognito User Pool Client
- Tablas de DynamoDB (tenant-sessions, tenant-usage)
- Roles y Políticas de IAM para Bedrock, Cognito y DynamoDB

**Anote las salidas (outputs)** de Terraform:
- `cognito_user_pool_id`
- `cognito_client_id`

### Paso 3: Configurar la Aplicación

#### 3.1 Crear un Entorno Virtual
```bash
python3 -m venv venv
source venv/bin/activate  # En Windows: venv\Scripts\activate
```

#### 3.2 Instalar Dependencias
```bash
pip install -r requirements.txt
```

#### 3.3 Configurar las Variables de Entorno
```bash
# Cree el archivo .env con las salidas de Terraform
cat > .env << EOF
COGNITO_USER_POOL_ID=<FROM_TERRAFORM_OUTPUT>
COGNITO_CLIENT_ID=<FROM_TERRAFORM_OUTPUT>
BEDROCK_AGENT_ID=<BEDROCK_AGENT_ID>
SESSIONS_TABLE=tenant-sessions
USAGE_TABLE=tenant-usage
AWS_REGION=us-east-1
EOF
```

#### 3.4 Configurar el Frontend
Actualice `frontend/index.html` con sus credenciales de Cognito:

```javascript
// Encuentre el objeto CONFIG alrededor de la línea 529 y actualice:
const CONFIG = {
    userPoolId: 'YOUR_USER_POOL_ID',      // Reemplace con la salida de Terraform
    clientId: 'YOUR_CLIENT_ID',            // Reemplace con la salida de Terraform
    region: 'us-east-1',
    apiUrl: 'http://localhost:8000'
};
```

**Importante**: Reemplace `YOUR_USER_POOL_ID` y `YOUR_CLIENT_ID` con los valores reales de la salida de Terraform.

### Paso 4: Ejecutar la Aplicación
```bash
python run.py
```

La aplicación estará disponible en: **http://localhost:8000**

### Paso 5: Crear el Primer Usuario
1. Abra el navegador en `http://localhost:8000`
2. Haga clic en "Create Account" (Crear cuenta)
3. Complete los detalles de registro
4. Seleccione la organización y el nivel de suscripción
5. Elija el rol "Admin" para obtener acceso de administrador
6. Verifique el correo electrónico con el código enviado a su dirección
7. ¡Inicie sesión y comience a chatear!

## 🔐 Autenticación y Multiinquilino

### Estructura del Token JWT
```json
{
  "sub": "user-uuid",
  "email": "user@company.com",
  "custom:tenant_id": "acme-corp",
  "custom:subscription_tier": "premium",
  "cognito:groups": ["acme-corp-admins"],
  "exp": 1703123456
}
```

### Aislamiento de Inquilinos
- **IDs de Sesión**: `{tenant_id}-{subscription_tier}-{user_id}-{session_id}`
- **Particionamiento en DynamoDB**: Todos los datos particionados por tenant_id
- **Contexto de Bedrock Agent Core**: La información del inquilino se pasa en los atributos de sesión
- **Acceso de Administrador**: Grupos de Cognito para privilegios de administrador específicos del inquilino

### Flujo de Registro de Usuarios
1. **Registro de Autoservicio**: Los usuarios se registran seleccionando el inquilino y el nivel de suscripción
2. **Selección de Rol de Administrador**: Rol de administrador opcional durante el registro
3. **Verificación de Correo Electrónico**: Se requiere la verificación de correo electrónico de Cognito
4. **Asignación Automática de Grupos**: Los usuarios administradores se agregan automáticamente al grupo `{tenant-id}-admins`
5. **Generación de JWT**: El inicio de sesión genera un JWT con el contexto del inquilino y la pertenencia a grupos

## 🤖 Integración con Bedrock Agent Core Runtime

### Paso del Contexto del Inquilino

**El mecanismo central**: El contexto del inquilino se pasa a Bedrock Agent Core Runtime a través de los atributos de sesión en cada invocación. Esto permite:
- **Enrutamiento multiinquilino**: Un solo agente da servicio a múltiples organizaciones
- **Aplicación de la suscripción**: El comportamiento del agente se adapta según el nivel de suscripción del inquilino
- **Atribución de costos**: Todas las trazas incluyen tenant_id para una facturación precisa
- **Aislamiento de datos**: El agente mantiene un contexto separado por inquilino

```python
session_state = {
    "sessionAttributes": {
        "tenant_id": "acme-corp",
        "user_id": "user-123",
        "subscription_tier": "premium",
        "organization_type": "enterprise",
        "user_role": "admin"
    },
    "promptSessionAttributes": {
        "request_type": "agentic_query",
        "enable_planning": "true",
        "tenant_context": "acme-corp enterprise user",
        "cost_tracking": "enabled"
    }
}
```

### Invocación del Agente con Observabilidad

```python
response = bedrock_agent_runtime.invoke_agent(
    agentId=AGENT_ID,
    agentAliasId=ALIAS_ID,
    sessionId=f"{tenant_id}-{user_id}-{session_id}",
    inputText=message,
    sessionState=session_state,
    enableTrace=True  # Habilitar trazas de observabilidad
)

# Procesar la respuesta y extraer las trazas
for event in response['completion']:
    if 'trace' in event:
        # Capturar trazas de planificación, razonamiento y acción
        trace_data = event['trace']
        store_trace_for_observability(trace_data, tenant_id, user_id)
```

### Funcionalidades de Observabilidad de Bedrock Agent Core

#### Captura de Trazas
- **Trazas de Planificación**: Proceso de razonamiento paso a paso del agente
- **Invocaciones de Acciones**: Llamadas a herramientas, solicitudes de API y ejecuciones de funciones
- **Consultas a la Base de Conocimiento**: Operaciones de generación aumentada por recuperación (RAG)
- **Pasos de Orquestación**: Coordinación de flujos de trabajo de múltiples pasos
- **Manejo de Errores**: Puntos de falla y lógica de reintentos

#### Atribución de Costos por Inquilino

**Crítico para el Multiinquilino**: Cada traza y métrica incluye tenant_id, lo que permite una atribución precisa de costos:

```python
# Rastrear costos por inquilino y usuario
await cost_service.track_usage_cost(
    tenant_id=tenant_id,  # Identifica a qué organización facturar
    user_id=user_id,      # Rastrea el consumo individual del usuario
    session_id=session_id,
    metric_type="bedrock_input_tokens",
    value=input_tokens,
    model_id=model_id,
    trace_id=trace_id
)
```

**Flujo de Atribución de Costos:**
1. El usuario realiza una solicitud con un JWT que contiene tenant_id
2. El contexto del inquilino se pasa a Agent Core Runtime
3. El agente procesa la solicitud y genera trazas
4. Todas las trazas se etiquetan con tenant_id y user_id
5. Los costos se calculan y atribuyen automáticamente por inquilino
6. Los paneles de administración muestran los costos por inquilino y por usuario

#### Monitoreo del Rendimiento
- **Tiempos de Respuesta**: Latencia de extremo a extremo por solicitud
- **Uso de Tokens**: Tokens de entrada/salida por inquilino y usuario
- **Patrones de Llamadas a la API**: Frecuencia y distribución de las invocaciones del agente
- **Cumplimiento de la Suscripción**: Aplicación en tiempo real de los límites del nivel

### Gestión de Sesiones
- **Aislamiento de Inquilinos**: Cada sesión está vinculada a un inquilino específico con separación total de datos
- **Seguimiento de Usuarios**: Sesiones individuales de usuarios dentro de los inquilinos
- **Preservación del Contexto**: Los atributos de sesión se mantienen a lo largo de los turnos de conversación
- **Almacenamiento de Trazas**: Todas las trazas del agente se almacenan en DynamoDB para auditoría y análisis
- **Límites de Suscripción**: Aplicación en tiempo real de los límites de uso basados en el nivel

### Beneficios del Multiinquilino

**¿Por qué pasar tenant_id en tiempo de ejecución?**
1. **Eficiencia de Costos**: Un solo despliegue de Bedrock Agent Core en lugar de uno por inquilino
2. **Gestión Simplificada**: Un único agente para mantener y actualizar
3. **Escalado Flexible**: Agregar nuevos inquilinos sin cambios en la infraestructura
4. **Facturación Precisa**: Atribución automática de costos por inquilino
5. **Control de Suscripción**: Diferentes funcionalidades/límites por nivel de inquilino
6. **Registro de Auditoría**: Traza completa de qué inquilino accedió a qué



## 💰 Sistema de Atribución de Costos

### Por Qué Importa la Atribución de Costos en el Multiinquilino

Cuando múltiples inquilinos comparten el mismo Bedrock Agent Core Runtime, una atribución precisa de costos es fundamental para:
- **Facturación**: Cobrar a cada inquilino por su uso real
- **Analítica**: Comprender qué inquilinos consumen más recursos
- **Optimización**: Identificar oportunidades de ahorro de costos por inquilino
- **Transparencia**: Mostrar a los clientes exactamente por qué están pagando

### Seguimiento Granular de Costos
- **Costos por Inquilino**: Desglose completo de costos por inquilino (habilitado por tenant_id en las trazas)
- **Costos por Usuario**: Consumo individual del usuario dentro de los inquilinos
- **Costos por Servicio**: Desglose por modelos de Bedrock, orquestación de Agent Runtime, etc.
- **Acceso Solo para Administradores**: Informes de costos restringidos a los administradores del inquilino
- **Atribución en Tiempo Real**: Los costos se rastrean a medida que se procesan las solicitudes

### Categorías de Costos
```json
{
  "bedrock_models": {
    "input_tokens": "Varies by model (Claude, Titan, etc.)",
    "output_tokens": "Varies by model (Claude, Titan, etc.)"
  },
  "agent_runtime": {
    "orchestration": "$0.001 per invocation",
    "tool_execution": "$0.0005 per action"
  },
  "observability": {
    "trace_storage": "DynamoDB costs",
    "metrics_tracking": "Included"
  }
}
```

### Informes de Costos para Administradores
- **Costo Total del Inquilino**: Costos totales con desglose por servicio (modelos de Bedrock, Agent Runtime, etc.)
- **Análisis por Usuario**: Patrones de consumo individual del usuario con correlación de trazas
- **Tendencias por Servicio**: Patrones de uso diarios y análisis de picos
- **Atribución Basada en Trazas**: Seguimiento de costos vinculado a las trazas de ejecución del agente
- **Informes Integrales**: Todas las dimensiones de costos en una sola vista con perspectivas de observabilidad

## 📊 Niveles de Suscripción y Límites de Uso

### Comparación de Niveles
| Funcionalidad | Básico (Gratis) | Avanzado ($29/mes) | Premium ($99/mes) |
|---------|--------------|-------------------|------------------|
| Mensajes Diarios | 50 | 200 | 1,000 |
| Mensajes Mensuales | 1,000 | 5,000 | 25,000 |
| Sesiones Concurrentes | 1 | 3 | 10 |
| Herramientas de Clima | ❌ | ✅ Básico | ✅ Completo |
| Informes de Costos | ❌ | ❌ | ✅ Administrador |
| Duración de la Sesión | 30 min | 60 min | 240 min |

### Aplicación de Uso
- **Límites en Tiempo Real**: Los endpoints de la API verifican el uso antes de procesar
- **Funcionalidades Basadas en Nivel**: Herramientas MCP y acceso de administrador controlados por la suscripción
- **Seguimiento de Uso**: Todo el consumo se almacena en DynamoDB para la facturación

## 🔧 Endpoints de la API

### Endpoints que Requieren Autenticación
```bash
# Chatear con Bedrock Agent Core
POST /api/chat
Authorization: Bearer <jwt-token>

# Gestión de Sesiones
POST /api/sessions
GET /api/tenants/{tenant_id}/sessions

# Uso y Analítica
GET /api/tenants/{tenant_id}/usage
GET /api/tenants/{tenant_id}/subscription

# Informes de Costos (Nivel de Usuario)
GET /api/tenants/{tenant_id}/costs
GET /api/tenants/{tenant_id}/users/{user_id}/costs


```

### Endpoints Solo para Administradores
```bash
# Atribución Granular de Costos (Solo Administradores)
GET /api/admin/tenants/{tenant_id}/overall-cost
GET /api/admin/tenants/{tenant_id}/per-user-cost
GET /api/admin/tenants/{tenant_id}/service-wise-cost
GET /api/admin/tenants/{tenant_id}/users/{user_id}/service-cost
GET /api/admin/tenants/{tenant_id}/comprehensive-report

# Gestión de Administración
GET /api/admin/my-tenants
POST /api/admin/add-to-group
```

## 🗄️ Almacenamiento de Datos

### Tablas de DynamoDB

**Tabla de Sesiones** (`tenant-sessions`)
```json
{
  "session_key": "acme-corp-premium-user123-uuid",
  "tenant_id": "acme-corp",
  "user_id": "user123",
  "subscription_tier": "premium",
  "created_at": "2024-01-01T00:00:00Z",
  "message_count": 15
}
```

**Tabla de Métricas de Uso** (`tenant-usage`)
```json
{
  "tenant_id": "acme-corp",
  "timestamp": "2024-01-01T00:00:00Z",
  "user_id": "user123",
  "metric_type": "bedrock_input_tokens",
  "value": 150,
  "session_id": "uuid",
  "model_id": "anthropic.claude-3-haiku-20240307-v1:0",
  "agent_id": "BAUOKJ4UDH"
}
```

### Modelos de Bedrock Compatibles

Esta aplicación admite todos los modelos fundacionales de AWS Bedrock, incluyendo:
- **Modelos Claude de Anthropic**: Claude 3 Haiku, Claude 3 Sonnet, Claude 3.5 Sonnet, Claude 3 Opus, Claude Sonnet 4, Claude Sonnet 4.5
- **Modelos Titan de Amazon**: Titan Text Express, Titan Text Lite, Titan Embeddings
- **Modelos de AI21 Labs**: Jurassic-2 Ultra, Jurassic-2 Mid
- **Modelos de Cohere**: Command, Command Light
- **Modelos de Meta**: Llama 2, Llama 3
- **Modelos de Stability AI**: Stable Diffusion (para generación de imágenes)

Configure el ID del modelo en su aplicación según su caso de uso y requisitos de costos.

## 🌐 Funcionalidades del Frontend

### Interfaz de Usuario
- **Registro con Cognito**: Registro de autoservicio de usuarios con selección de inquilino
- **Selección de Rol de Administrador**: Privilegios de administrador opcionales durante el registro
- **Chat en Tiempo Real**: Interfaz de chat tipo WebSocket con Bedrock Agent Core
- **Panel de Uso**: Visualización de los límites de suscripción y el uso actual
- **Informes de Costos**: Visibilidad de costos a nivel de usuario
- **Integración de Clima**: Consultas meteorológicas en lenguaje natural

### Interfaz de Administración
- **Analítica de Costos**: Informes y tendencias de costos granulares
- **Gestión de Usuarios**: Ver todos los usuarios del inquilino y su consumo
- **Análisis de Servicios**: Desglose por uso de servicios de AWS
- **Informes Integrales**: Análisis de costos multidimensional

## 🔍 Monitoreo y Analítica

### Seguimiento de Uso
- **Métricas en Tiempo Real**: Se rastrea cada llamada a la API, uso de tokens y consumo de servicios
- **Analítica de Inquilinos**: Patrones de uso agregados por inquilino
- **Atribución de Costos**: Cálculo y atribución automáticos de costos
- **Monitoreo del Rendimiento**: Tiempos de orquestación de Bedrock Agent Core, latencia de inferencia del modelo y tasas de éxito
- **Análisis de Trazas**: Visibilidad completa de los pasos de planificación, razonamiento y ejecución del agente

### Análisis de Trazas
- **Trazas de Bedrock Agent Core**: Trazas completas de orquestación, planificación y razonamiento
- **Trazas de Ejecución de Acciones**: Invocaciones de herramientas, llamadas a la API y ejecuciones de funciones
- **Trazas de la Base de Conocimiento**: Operaciones RAG y patrones de recuperación
- **Trazas de Rendimiento**: Desglose de la latencia por paso de orquestación
- **Analítica de Sesiones**: Patrones de interacción del usuario y duración de la sesión
- **Trazas de Errores**: Análisis de fallas y perspectivas de depuración



## 📈 Funcionalidades Avanzadas

### Integración de Clima en Tiempo Real
- **Datos de API en Vivo**: Información meteorológica en tiempo real de OpenWeatherMap
- **Procesamiento de Lenguaje Natural**: Bedrock Agent Core convierte los datos meteorológicos en respuestas conversacionales
- **Acceso Basado en Suscripción**: Herramientas de clima disponibles según el nivel de suscripción
- **Manejo de Errores**: Mecanismos de respaldo elegantes ante fallas de la API

### Optimización de Costos
- **Facturación Basada en Uso**: Pague solo por el consumo real
- **Límites Basados en Nivel**: Prevenga costos descontrolados con los límites de suscripción
- **Visibilidad para Administradores**: Transparencia total de costos para los administradores del inquilino
- **Atribución por Servicio**: Comprenda los costos por uso de servicios de AWS

### Arquitectura Multiinquilino
- **Aislamiento Total**: No es posible el acceso a datos entre inquilinos
- **Diseño Escalable**: Agregue nuevos inquilinos sin cambios en la infraestructura
- **Segregación de Administradores**: Acceso administrativo específico del inquilino
- **Analítica de Uso**: Patrones de uso por inquilino y oportunidades de optimización

## 🎥 Video de Demostración

Vea la aplicación en acción:

![Demostración](Demo.gif)

La demostración muestra:
- Registro y autenticación de usuarios con Cognito
- Interfaz de chat multiinquilino con Bedrock Agent Core Runtime
- Consultas meteorológicas en tiempo real usando herramientas MCP
- Gestión del nivel de suscripción y seguimiento de uso
- Paneles de atribución de costos y analítica para administradores

## 🧹 Limpieza

Para eliminar todos los recursos de AWS y evitar cargos:

### Destruir la Infraestructura de Terraform
```bash
cd infra/terraform
terraform destroy
```

Esto eliminará:
- Cognito User Pool y Client
- Tablas de DynamoDB
- Roles y Políticas de IAM

### Detener la Aplicación
```bash
# Presione Ctrl+C en la terminal que ejecuta la aplicación
# Desactive el entorno virtual
deactivate
```

## 🛠️ Desarrollo Local

```bash
# Iniciar el servidor de desarrollo
python run.py

# Ejecutar con registro de depuración
DEBUG=true python run.py

# Cambiar el modelo de Bedrock (edite app/bedrock_service.py)
# Actualice MODEL_ID a cualquier modelo de Bedrock compatible
# Consulte la Consola de AWS Bedrock para ver los modelos disponibles en su región
```

## 📝 Notas

- **Solo para Desarrollo**: La aplicación se ejecuta en `localhost:8000`, no está lista para producción
- **Código de Ejemplo**: Úselo como referencia para construir sus propias aplicaciones de IA multiinquilino
- **Precios de Bedrock**: Varían según el modelo: Claude 3 Haiku (~$0.25/1M tokens de entrada), Sonnet (~$3/1M tokens de entrada)
- **Bedrock Agent Core Runtime**: Se aplican costos adicionales de orquestación
- **DynamoDB**: Facturación bajo demanda (pago por solicitud)
- **Cognito**: Gratis para los primeros 50,000 usuarios activos mensuales
- **Almacenamiento de Datos**: Todos los datos se almacenan en su cuenta de AWS con aislamiento de inquilinos
- **Selección de Modelo**: Configure el ID del modelo en `app/bedrock_service.py`

### IDs de Modelo de Ejemplo
```python
# IDs de Modelo de Bedrock de ejemplo (consulte la Consola de AWS Bedrock para las versiones más recientes)
CLAUDE_3_HAIKU = "anthropic.claude-3-haiku-20240307-v1:0"
CLAUDE_3_SONNET = "anthropic.claude-3-sonnet-20240229-v1:0"
CLAUDE_3_5_SONNET = "anthropic.claude-3-5-sonnet-20240620-v1:0"
TITAN_TEXT_EXPRESS = "amazon.titan-text-express-v1"
LLAMA_2_13B = "meta.llama2-13b-chat-v1"
COMMAND = "cohere.command-text-v14"
```

## 🎯 Casos de Uso

Este código de ejemplo demuestra patrones para:

### Aplicaciones de IA SaaS Multiinquilino
- **Un Solo Agente, Múltiples Clientes**: Un Bedrock Agent Core Runtime da servicio a todas las organizaciones
- **Experiencias Específicas del Inquilino**: Respuestas personalizadas según el contexto del inquilino
- **Niveles de Suscripción**: Básico, Avanzado y Premium con diferente acceso a funcionalidades
- **Facturación Basada en Uso**: Atribución precisa de costos por inquilino para la facturación

### Plataformas de IA Empresariales
- **Aislamiento por Departamento**: Diferentes departamentos como inquilinos separados
- **Atribución a Centros de Costos**: Rastree los costos de IA por departamento/equipo
- **Acceso Basado en Roles**: Capacidades de administrador frente a usuario regular por inquilino
- **Cumplimiento y Auditoría**: Traza completa del acceso a los datos del inquilino

### Mercados de Servicios de IA
- **IA de Marca Blanca**: El mismo agente, diferente marca por inquilino
- **Precios Flexibles**: Diferentes niveles de suscripción con límites de uso
- **Transparencia de Costos**: Muestre a los clientes su consumo exacto de IA
- **Incorporación Escalable**: Agregue nuevos clientes sin cambios en la infraestructura

**Ventaja Clave**: Al pasar tenant_id en tiempo de ejecución a Bedrock Agent Core, puede dar servicio a inquilinos ilimitados desde un único despliegue de agente, manteniendo al mismo tiempo un aislamiento total y una atribución precisa de costos.
