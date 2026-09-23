# DP-800 Laboratorio 03 - Write advanced T-SQL queries

**Autor:** Christian Salguero Varas <br>
**Fecha:** 22/09/2026 - 23/09/2026 - 24/09/2026

---

## 1. Estimar el coste antes de desplegar

Para esta estimación necesitas estos tres componentes:
- Virtual Machines
- Managed Disks
- IP Addresses

A tener en cuenta: la calculadora está en euros (€) y Azure al tenerlo en Inglés está en dolares ($).

Configuramos la máquina con los valores indicados.

![Calculadora de coste de la máquina virtual](images/01_MaquinaVirtualCalculadora.png) <br>
![Calculadora de coste de la máquina virtual (vista detallada)](images/02_MaquinaVirtualCalculadora2.png)

Configuramos el disco con los valores indicados.

![Calculadora de coste del disco de almacenamiento](images/03_DiscoCalculadora.png)

Configuramos la IP pública.

![Calculadora de coste de la dirección IP pública](images/04_DireccionIPCalculadora.png)

Cálculo de los tres escenarios:

| Escenario | Horas de cómputo | Coste cómputo (mensual) | Coste disco | Coste IP | Total estimado |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **A. VM encendida todo el laboratorio** | 72 | 2,82€ | 0,20€ | 0,31€ | **3,33€** |
| **B. VM con apagado automático nocturno (≈ 12 h/día)** | 36 | 1,41€ | 0,20€ | 0,31€ | **1,92€** |
| **C. VM encendida solo 8 h en total** | 8 | 0,31€ | 0,20€ | 0,31€ | **0,82€** |


## Creación del Resource Group

En Azure accedemos a `Resource groups` y creamos el grupo de recursos con la siguiente información:

![Creación del Grupo de Recursos en Azure](images/05_CrearResourceGroup.png)

Agregamos sus Tags:

![Configuración de Tags en el Grupo de Recursos](images/06_TagsResourceGroup.png)

Cambiamos el scope para crear el Budget correctamente.

![Selección de ámbito en Cost Management](images/07_CostManagementScope.png)

Creamos el Budget con la información necesaria.

![Creación del presupuesto (Budget) en Azure](images/08_CrearBudget.png)

Configuramos las alertas.

![Configuración de alertas para el presupuesto](images/09_AlertsBudget.png)

## Deplegar la Máquina Virtual

Definimos los primeros valores de la máquina virtual. Como entre nuestras regiones permitidas no está `West Europe` he buscado cuál tenía disponible la instancia deseada y esa era `Spain Central`.

![Inicio del proceso de creación de la Máquina Virtual](images/10_CreacionMaquinaVirtual.png)

Comparamos la instancia entre la calculadora y Azure y vemos que son la misma con mismo valor (ambas están en dolares $).

![Comparación de estimación entre la calculadora y Azure](images/11_ComparacionAproximadoAzureCalculadora.png)

Definimos los valores del disco.

![Configuración de la Máquina Virtual - Pestaña Discos](images/12_CreacionMaquinaVirtual2.png)

Definimos los valores de red.

![Configuración de la Máquina Virtual - Pestaña Redes](images/13_CreacionMaquinaVirtual3.png) <br>
![Configuración de la Máquina Virtual - Pestaña Redes](images/14_CreacionMaquinaVirtual4.png)

No podemos marcas la opción de `Auto ShutDown` ya que no está disponible en `Spain Central`. Más adelante intento volver a activar esta opción y me da error.

![Configuración de la Máquina Virtual - Administración](images/15_CreacionMaquinaVirtual5.png)

Agregamos los Tags a la máquina virtual:

![Configuración de la Máquina Virtual - Tags](images/24_CreacionMaquinaVirtual7.png)

Revisamos que todo está correcto y creamos la máquina virtual. Aquí podemos ver como el consumo por hora es el mismo que en la calculadora.

![Revisión y validación previa a la creación de la VM](images/16_CreacionMaquinaVirtual6.png)

Nos descargamos la clave privada SSH.

![Descarga de la clave privada SSH (Key Pair)](images/17_DownloadPrivateKey.png)

Y la máquina virtual se empezará a crear y desplegar.

![Despliegue de la Máquina Virtual en progreso](images/18_CreandoMaquinaVirtual.png)

Máquina virtual creada correctamente.

![Confirmación de Máquina Virtual creada correctamente](images/19_MaquinaCreada.png)

| Dato | Valor |
| :--- | :--- |
| **Precio por hora mostrado por el portal** | 0.0456 USD/hr |
| **Precio por hora según tu estimación** | 0.046 USD/hr |
| **Fecha y hora de creación** | 17:01:47 22/09/2026 |

Con la máquina ya creada vuelvo a intentar agregar la orden `Auto ShutDown`.

![Intento de configuración de Auto-shutdown](images/20_AgregarAutoShutdown.png) <br>
![Ajustes de parámetros para el Auto-shutdown](images/21_AutoShutdownAgregado.png)

Cuando pulsamos en `Save` nos salta este error:

![Error de disponibilidad de recurso para Auto-shutdown en Spain Central](images/22_AutoShutdownError.png)

Que nos indica que la opción no está disponible en la región seleccionada.

## Verificar el etiquetado de todos los recursos

| Recurso | Tipo | ¿Tiene los 5 Tags? | ¿Genera coste? |
| :--- | :--- | :---: | :---: |
| **vm-practica04-ChristianSalguero** | Virtual machine | Sí | Sí |
| **vm-practica04-ChristianSalguero_OsDisk_1_8fbbd4cdfa48411181b5c94f8945a3fa** | Disk | Sí | Sí |
| **vm-practica04-ChristianSalguero-ip** | Public IP address | Sí | Sí |
| **vm-practica04-christiansalguero103** | Network interface | Sí | No |
| **vm-practica04-ChristianSalguero-nsg** | Network security group | Sí | No |
| **vnet-spaincentral-1** | Virtual network | No | No |

Comprobando cada apartado el único que no tenía las Tags era la `Virtual Network` entonces se los agregué a mano.

![Asignación de Tags en la Red Virtual (Virtual Network)](images/23_AgregarTagsVirtualNetwork.png)

## Primera revisión en Cost Analysis

| Recurso | Coste acumulado |
| :--- | :---: |
| **Máquina virtual** | 0.16€ |
| **Disco** | 0.03€ |
| **IP pública** | 0.05€ |
| **Otros** | 0.01€ |
| **Total (Actual Cost)** | **0.25€** |

![Revisión primer día](images/25_RevisionPrimerDia.png)

## Segunda revisión en Cost Analysis

| Recurso | Coste acumulado |
| :--- | :---: |
| **Máquina virtual** | € |
| **Disco** | € |
| **IP pública** | € |
| **Otros** | € |
| **Total (Actual Cost)** | **€** |

![Revisión segundo día](images/26_RevisionSegundoDia.png)

### Ver el coste por medidor

### Ver el coste diario

### Ver el coste por Tag

### Consultar la previsión

| Dato | Valor |
| :--- | :---: |
| **Actual Cost** |  |
| **Forecasted Cost (fin de mes)** |  |
| **Budget del laboratorio** |  |
| **¿Actual supera el Budget?** | Sí/No |
| **¿Forecast supera el Budget?** | Sí/No |

### Revisar las alertas

| Alerta | ¿Se ha activado? | Fecha y hora |
| :--- | :---: | :---: |
| **Actual 50%** |  |  |
| **Actual 80%** |  |  |
| **Actual 100%** |  |  |
| **Forecasted 100%** |  |  |

## Experimento: apagar no es lo mismo que desasignar

## Comparar estimación y coste real

| Concepto | Estimado (Sección 3) | Real (Cost Analysis) | Diferencia |
| :--- | :---: | :---: | :---: |
| **Cómputo** | € | € | € |
| **Disco** | € | € | € |
| **IP pública** | € | € | € |
| **Total** | **€** | **€** | **€** |

## Limpieza

## Informe final

## Ejercicio final

### 1. ¿Qué alertas se habrán activado con umbrales Actual 50 %, 80 %, 100 % y Forecasted 100 %?

### 2. ¿Por qué el Forecasted Cost es tan superior al Actual Cost?

### 3. ¿Qué parte del gasto de las 3 VMs detenidas se podría eliminar sin borrarlas?

### 4. Ordena estas acciones de mayor a menor impacto en el ahorro inmediato:

### 5. ¿Cuál de las acciones anteriores no ahorra dinero pero es imprescindible para analizarlo?

## Referencias oficiales

- Microsoft Learn — Tutorial: Create and manage budgets
    https://learn.microsoft.com/azure/cost-management-billing/costs/tutorial-acm-create-budgets
    
- Microsoft Learn — Cost Analysis
    https://learn.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis
    
- Microsoft Learn — Group and filter options in Cost Analysis and Budgets
    https://learn.microsoft.com/azure/cost-management-billing/costs/group-filter
    
- Microsoft Learn — Create a Linux virtual machine in the Azure portal
    https://learn.microsoft.com/azure/virtual-machines/linux/quick-create-portal
    
- Microsoft Learn — States and billing status of Azure Virtual Machines
    https://learn.microsoft.com/azure/virtual-machines/states-billing
    
- Microsoft Learn — Auto-shutdown a virtual machine
    https://learn.microsoft.com/azure/virtual-machines/auto-shutdown-vm
    
- Azure Pricing Calculator
    https://azure.microsoft.com/pricing/calculator/