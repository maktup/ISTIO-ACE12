<a name="br1"></a> 

**IMPLEMENTACIÓN DE: “ISTIO” (SERVICES MESH) &**

**“IBM APP CONNECT12” CONTENERIZADO.**

Este documento se enfoca en el ***PROCEDIMIENTO*** para la implementación de ***SERVICE MESH*** con ***ISTIO***, aplicado

El ***ÍNDICE*** propuesto será el siguiente:

a

***IBM APP CONNECT v12***.

***I.***

***DEFINICIONES.***

***II.***

***INSTALACIÓN & CONFIGURACIÓN.***

✓

***CREACIÓN DE INSTANCIA: "SERVICE MESH".***

✓

***CREACIÓN DE INSTANCIA: "IBM APP CONNECT v12".***

***III.***

***IV.***

***MANEJO DE: DASHBOARDs.***

***ESTRATEGIAS DE DESPLIEGUE & CONTROL DE VERSIONES.***

✓

***DEMO #1: (CANARY RELEASE).***

✓

✓

✓

***DEMO #2: (BLUE & GREEN DEPLOYMENT).***

***DEMO #3: (CONTROL DE TRÁFICO: “INGRESS GATEWAY”).***

***DEMO #4: (UNIFICANDO TODO).***

**DESCRIPCIÓN**

**DETALLE**

**I. DEFINICIONES:**



<a name="br2"></a> 

**¿QUÉ ES SERVICE MESH?:**

Es una capa de ***INFRAESTRUCTURA*** dedicada

la cual se puede agregar a nuestras ***APLICACIONES***,

con el objetivo de lograr: ***OBSERVABILIDAD, GESTIÓN***

***DE TRAFICO, SEGURIDAD.***

**¿QUÉ ES ISTIO?:**

Actualmente,

existen

varias

***IMPLEMENTACIONES***

***de SERVICE MESH,*** de las cuales la más importante

actualmente es: **ISTIO***.*

Es un ***SERVICE MESH*** OpenSource que se superpone

de manera transparente

a

las ***APLICACIONES***

***DISTRIBUIDAS***. Así mismo, **ISTIO** brinda:

▪

▪

***TRAZABILIDAD** de los **MICROSERVICIOS** & posibles*

***DEPENDENCIAS** entre ellos.*

*Comunicación segura entre **MICROSERVICIOS***

*en un clúster con cifrado **TLS**, autenticando*

*&*

*autorizando.*

▪

*Equilibrio de carga para **TRÁFICO: HTTP, TCP,***

***WebSocket**, etc.*

▪

▪

*Control detallado de **TRÁFICO**.*

*Métricas, registros*

*(entrada & salida).*

*&*

*seguimiento al **TRÁFICO**:*

▪

*Configuración de **PROXYS*** (**SIDECARs**)*, NO de manera*

*embebida en los **MICROSERVICIOS** (**OpenTracing**).*

**IMPORTANTE:** *“Conocer que la idea es que los*

***MICROSERVICIOS** manejen solo **LÓGICA DE NEGOCIO***

*&*

*que todo lo **ADICIONAL** como: tratamiento de*

*protocolos seguridad, etc, esté gestionado por el mismo:*

***SERVICE MESH**”.*



<a name="br3"></a> 

**¿CÓMO ES LA ARQUITECTURA DE ISTIO:**

Los ***COMPONENTES*** que maneja **ISTIO** son:

✓

**CONTROL PLANE:** Este es el encargado de gestionar

& configurar los ***PROXYS*** para el ***ENRUTAMIENTO***

del ***TRÁFICO*** en base a las ***REGLAS*** definidas.

✓

**DATA PLANE:** Compuesto por un conjunto de ***PROXYS***

inteligentes **(ENVOY)** creados en ***CONTAINERs***

&

desplegados como **SIDECARs** en los ***PODs***,

estos controlan toda la ***COMUNICACIÓN*** entre los

***MICROSERVICIOS*** & recopilan **TELEMETRÍA** de todo

el ***TRÁFICO*** del ***SERVICE MESH***.

Dicha

**TELEMETRÍA**

es

una

característica

de

***OBSERVABILIDAD*** que brinda **ISTIO**, para ***OBSERVAR***

el comportamiento de los ***MICROSERVICIOS***:

✓

✓

*Métricas (**LATENCIA, ERRORES, SATURACIÓN**).*

*Rastros Distribuidos (**FLUJO DE TRÁFICO** de*

***MICROSERVICIOS**).*



<a name="br4"></a> 

**II. INSTALACIÓN & CONFIGURACIÓN:**

Inicialmente se quiere ***VALIDAR*** la versión de **OPENSHIFT** que

se tenga instalado.

En este caso se ***VISUALIZA***:

✓

✓

***VERSIÓN:** 4.12.44*

***CANAL:** stable-412*

**IMPORTANTE:** *“Hay que considerar que **INSTIO** soporta el **OPENSHIFT***

*con las últimas versiones”*.



<a name="br5"></a> 

Luego, se procede a crear los **2 *NAMESPACES*** llamados:

*$ oc create ns istio-system*

*$ oc create ns ace-istio*



<a name="br6"></a> 

Luego,

se

procede

a

crear

los

***OPERATORs***

asociados

al ***NAMESPACE***: *istio-system*.

Primero se instalará el ***OPERATOR*** llamado:

✓

✓

✓

***NOMBRE:** OpenShift Service Mesh*

***VERSIÓN:** 2.4.5-0*

***MODO INSTALACIÓN:** All Namespaces on the cluster*

**IMPORTANTE:** *“La **INSTALACIÓN** podrá ser accedida desde todos*

*los **NAMESPACES**, pero donde se instalará principalmente será*

*en el **NAMESPACE**:* openshift-operators*”.*



<a name="br7"></a> 

Luego, dentro del ***NAMESPACE***: *istio-system*, se procede a instalar

el ***OPERATOR*** llamado:

✓

✓

✓

***NOMBRE:** Kiali Operator*

***VERSIÓN:** 1.65.11*

***MODO INSTALACIÓN:** All Namespaces on the cluster*

**IMPORTANTE:** *“La **INSTALACIÓN** podrá ser accedida desde todos*

*los **NAMESPACES**, pero donde se instalará principalmente será*

*en el **NAMESPACE**:* openshift-operators*”.*



<a name="br8"></a> 

Luego, dentro del ***NAMESPACE***: *istio-system*, se procede a instalar

el ***OPERATOR*** llamado:

✓

✓

✓

***NOMBRE:** Red Hat Openshift distribuited tracing platform*

***VERSIÓN:** 1.47.1-5*

***MODO INSTALACIÓN:** All Namespaces on the cluster*

**IMPORTANTE:** *“La **INSTALACIÓN** podrá ser accedida desde todos*

*los **NAMESPACES**, pero donde se instalará principalmente será*

*en el **NAMESPACE**:* openshift-distribuited-tracing*”.*



<a name="br9"></a> 

Luego, se procede

a

validar los ***OPERATORs*** instalados dentro

del ***NAMESPACE***: *istio-system* son:

▪ *OpenShift Service Mesh.*

▪ *Kiali Operator.*

▪ *Red Hat Openshift distribuited tracing platform.*

Luego,

se

procede

a

crear

los

***OPERATORs***

asociados

al ***NAMESPACE***: *ace-istio*.

Primero se instalará el ***OPERATOR*** llamado:

✓

✓

***NOMBRE:** IBM App Connect*

***VERSIÓN:** 10.0.1*

**A. CREACIÓN DE INSTANCIAS: “SERVICE MESH”**

Se procede a ingresar en el ***NAMESPACE***: ***istio-system*** & al ***OPERATOR***:

**Red Hat Openshift Service Mesh** & en la pestaña: ***Istio Service Mesh***

***Control Plane***, se procede a crear una ***INSTANCIA*** del ***RECURSO***:

**ServiceMeshControlPlane**:

*apiVersion: maistra.io/v2*

*kind: **ServiceMeshControlPlane***

*metadata:*

*name: **basic***

*namespace: istio-system*

*spec:*

***addons:***

***grafana:***

*enabled: true*

***jaeger:***

*install:*

*storage:*

*type: Memory*

***kiali:***

*enabled: true*

***prometheus:***

*enabled: true*

*policy:*

*type: Istiod*

*profiles:*

*- default*



<a name="br10"></a> 

*telemetry:*

*type: Istiod*

*tracing:*

*sampling: 10000*

*type: Jaeger*

*version: v2.4*

**IMPORTANTE:** *“Hay que considerar que el **ESTADO** debe mostrarse*

*como se muestra en la **IMAGEN**:* **Installed, Reconcilled, Ready***”*.

Luego, dentro del mismo ***OPERATOR***: **Red Hat Openshift Service Mesh**

se ingresa en la pestaña: ***Istio Service Mesh Member Roll*** & se procede

a crear una ***INSTANCIA*** del ***RECURSO***: **ServiceMeshMemberRoll**:

*apiVersion: maistra.io/v1*

*kind: **ServiceMeshMemberRoll***

*metadata:*

*name: **default***

*namespace: istio-system*

*spec:*

*members:*

*- ace-istio*

Aquí se hará ***REFERENCIA*** a los ***NAMESPACES*** que se requiera

que se ***INYECTE ISTIO***, en este caso donde se tienen instalados

los ***MICROSERVICIOS*** en ***ACE12***.

**IMPORTANTE:** *“Hay que considerar que el **ESTADO** debe mostrarse*

*como se muestra en la **IMAGEN**:* **Ready***”.*



<a name="br11"></a> 

Finalmente, hay que considerar que se debe ***VALIDAR*** que la

***CREACIÓN*** de las ***INSTANCIAS*** en el ***OPERATOR***:

***2***

**Red Hat Openshift Service Mesh**, generarán los ***ROUTEs***

en la ***IMAGEN***, de los cuales el más importante para la prueba

es el: istio-ingressgateway.

**B. CREACIÓN DE INSTANCIAS: “IBM APP CONNECT v12”**

Antes que nada, se debe considerar el ***CREAR* 2 DASHBOARDs**,

uno por cada ***MODALIDAD*** manejada:

✓

✓

***INTEGRATION-SERVER:** inst-dashboard-**is**-istio*

***INTEGRATION-RUNTIME:** inst-dashboard-**ir**-istio*



<a name="br12"></a> 

Luego, se procede

a

ingresar en el ***NAMESPACE***: ***ace-istio***

&

al ***OPERATOR***: **IBM App Connect**

&

se ingresa en la pestaña:

***Integration Server*** & se procede a crear una ***INSTANCIA*** del ***RECURSO***:

**IntegrationServer**:

*apiVersion: appconnect.ibm.com/v1beta1*

*kind: **IntegrationServer***

*metadata:*

*name: **dummy-csm-rest-topdown-01a***

*namespace: ace-istio*

*spec:*

*enableMetrics: true*

*license:*

*accept: true*

*license: L-SEWB-GH63KR*

*use: CloudPakForIntegrationNonProductionFREE*

***annotations:***

***sidecar.istio.io/inject: 'true'***

*pod:*

*containers:*

*runtime:*

*resources:*

*limits:*

*cpu: 300m*

*memory: 368Mi*

*requests:*

*cpu: 300m*

*memory: 368Mi*

*adminServerSecure: true*

*router:*

*timeout: 120s*

*designerFlowsOperationMode: disabled*

*createDashboardUsers: true*

*service:*

*endpointType: http*

*version: '12.0'*

*replicas: 1*

*barURL: >-*

*https://inst-dashboard-is-istio-*

*dash:3443/v1/directories/dummy\_csm\_rest\_topdown?2a2ccba2-2125-462e-927b-*

*ee51c51bf849*

*configurations: []*

***labels:***

***sidecar.istio.io/inject: 'true'***

En este caso el despliegue del ***MICROSERVICIO*** en el **IntegrationServer**

se realizará **2** veces *(para tener **2 MICROSERVICIOS** desplegados)*.

Estos son:

✓

✓

*dummy-csm-rest-topdown-**01a***

*dummy-csm-rest-topdown-**01b***

No olvidar en el ***Script*** las consideraciones del ***LABEL*** y/o ***ANOTACIÓN***

con el: ***sidecar.istio.io/inject: 'true'***, que es lo que ***INYECTARÁ*** la funcionalidad

de ***ISTIO***.



<a name="br13"></a> 

**IMPORTANTE:** *“El **barURL** debe estar asociado al **ENDPOINT***

*del **COMPILADO** .bar, existente en el **DASHBOAR** de tipo:*

***INTEGRATION-SERVER**”.*



<a name="br14"></a> 

Luego, se procede

a

ingresar en el ***NAMESPACE***: ***ace-istio***

en el ***OPERATOR***: **IBM App Connect** & se selecciona la pestaña:

***Integration Runtime*** & se procede a crear una ***INSTANCIA*** del ***RECURSO***:

**IntegrationRuntime**:

*apiVersion: appconnect.ibm.com/v1beta1*

*kind: **IntegrationRuntime***

*metadata:*

*name: **dummy-csm-rest-topdown-01a-ir***

*namespace: ace-istio*

*spec:*

*license:*

*accept: true*

*license: L-SEWB-GH63KR*

*use: CloudPakForIntegrationNonProductionFREE*

*replicas: 1*

*template:*

*spec:*

*containers:*

*- name: runtime*

*resources:*

*requests:*

*cpu: 300m*

*memory: 368Mi*

*metadata:*

***labels:***

***sidecar.istio.io/inject: 'true'***

*version: '12.0'*

*barURL:*

*- >-*

[*https://inst-dashboard-ir-istio-dash.ace-*](https://inst-dashboard-ir-istio-dash.ace-istio:3443/v1/directories/dummy_csm_rest_topdown?0333a2a5-bcbb-4417-b212-b238fc48934d)

[*istio:3443/v1/directories/dummy_csm_rest_topdown?0333a2a5-bcbb-4417-b212-b238fc48934d*](https://inst-dashboard-ir-istio-dash.ace-istio:3443/v1/directories/dummy_csm_rest_topdown?0333a2a5-bcbb-4417-b212-b238fc48934d)

En este caso el despliegue del ***MICROSERVICIO*** en el **IntegrationRuntime**

se realizará **2** veces *(para tener **2 MICROSERVICIOS** desplegados)*.

Estos son:

✓

✓

*dummy-csm-rest-topdown-**01a**-ir*

*dummy-csm-rest-topdown-**02b**-ir*

No olvidar en el ***Script*** las consideraciones del ***LABEL*** y/o ***ANOTACIÓN***

con el: ***sidecar.istio.io/inject: 'true'***, que es lo que ***INYECTARÁ*** la funcionalidad

de ***ISTIO***.

**IMPORTANTE:** *“El **barURL** debe estar asociado al **ENDPOINT***

*del **COMPILADO** .bar, existente en el **DASHBOAR** de tipo:*

***INTEGRATION-RUNTIME**”.*



<a name="br15"></a> 

Finalmente, luego de la creación de las ***INSTANCIAS*** de:

**INTEGRATION-SERVER INTEGRATION-RUNTIME**, para los

&

***MICROSERVICIOS***, se ***VALIDA*** que los ***PODs*** (dummy-csm) tengan

el ***SEGUNDO CONTENEDOR***, asociado a ***PROXY*** de ***ISTIO***, ingresando

en ***PODs/DETAIL***:

En la ***IMAGEN*** se muestra que independientemente de la ***MODALIDAD***

de despliegue que se tenga: **INTEGRATION-SERVER** o **INTEGRATION-**

**RUNTIME**, el ***CONTAINER*** con el ***PROXY ISTIO*** es automáticamente

generado.

Con esto ***ISTIO*** ya está aplicado

en ***IBM APP CONNECT 12***.

a

nuestros ***MICROSERVICIOS***

**III. MANEJO DE: DASHBOARDs**



<a name="br16"></a> 

Con relación a los **DASHBOARDs** es importante conocer la *lista* de ***IPs***

& ***PÚERTOs*** que internamente maneja ***ISTIO***. Para ello, de debe

ingresar el ***comando***:

*$ kubectl get **services** -n istio-system*

Así mismo, para identificar las ***URLs*** de los ***DASHBOARDs***

de las ***HERRAMIENTAS*** asociadas al trabajo de ***ISTIO***, se ingresa:

*$ kubectl get **routes** -n istio-system*



<a name="br17"></a> 

Luego, los **DASHBOARDs** generados de las ***HERRAMIENTAS***

para el acceso son:

**A. GRAFANA:**

*“Es una **HERRAMIENTA** de monitoreo que brinda visualizaciones*

*& paneles **GRÁFICOS** para las **MÉTRICAS** & de **MONITOREO***

*de clústeres”.*

✓

***URL:** https://grafana-istio-system.apps.tarkin.coc-ibm.com*

**B. JAEGER:**

*“Es una **HERRAMIENTA** de monitoreo, que tiene como **OBJETIVO***

*el seguimiento del **FLUJO DE INFORMACIÓN**, través de*

***Aplicaciones Distribuidas Microservicios**, permitiendo la*

*a*

***&***

***VISIBILIDAD** de cómo los datos son mostrados a través de diferentes*

***COMPONENTES** de una **ARQUITECTURA DISTRIBUIDA**”.*

✓

***URL:** https://jaeger-istio-system.apps.tarkin.coc-ibm.com*



<a name="br18"></a> 

**C. PROMETHEUS:**

*“Es una **HERRAMIENTA** de monitoreo & **ALERTA**, que tiene como*

***OBJETIVO** de recopilar **MÉTRICAS** de: **Aplicaciones Distribuidas***

***&***

***Microservicios,** especialmente para los basados en*

***CONTENEDORES**”.*

✓

***URL:** https://prometheus-istio-system.apps.tarkin.coc-ibm.com*

**D. KIALI:**

*“Es una **HERRAMIENTA** de monitoreo & **OBSERVABILIDAD** diseñada*

*específicamente para **SERVICE MESH** como **ISTIO**, proporciona una*

*capa de abstracción para las interacciones entre **Aplicaciones***

***Distribuidas & Microservicios**.*

▪

▪

▪

Brinda una representación ***GRÁFICA*** de la ***ARQUITECTURA***

de ***MICROSERVICIOS***, mostrando las relaciones entre los

diferentes: ***SERVICIOS***, ***VERSIONES***, ***ROUTES*** & ***TRÁFICO***.

Brinda

un

mapa

de

***TOPOLOGÍA***

para

comprender

cómo se ***COMUNICAN*** los ***SERVICIOS*** entre sí con sus

***DEPENDENCIAS***.

Brinda un ***TRACING*** para seguir el flujo de un ***REQUEST**,*

a través de los diferentes ***SERVICIOS***, evitando cuellos de botella.

✓

***URL:** https://kiali-istio-system.apps.tarkin.coc-ibm.com*

**IV. ESTRATEGIAS DE DESPLIEGUE & CONTROL DE VERSIONES:**

**A. DEMO #1: (CANARY RELEASE):**



<a name="br19"></a> 

▪

▪

Esta ***ESTRATEGIA*** de ***IMPLEMENTACIÓN*** maneja una ***NUEVA VERSIÓN*** de una aplicación *(Servicio Microservicio)*, enfocada para el procesamiento del ***TRÁFICO***

*(antes de poderla **DISPONIBLE** en **PRODUCCIÓN**).*

Aquí en la nueva ***VERSIÓN*** de la aplicación en ***PRODUCCIÓN*** es denominada: **CANARY**, pero la idea es que solo una pequeña parte del ***TRÁFICO*** (5% o 10%)

sea dirigido hacia dicha versión.

▪

▪

Si esta versión **CANARY** funciona correctamente & sin problemas o errores, se puede ***INCREMENTAR*** gradualmente el ***TRÁFICO*** hacia dicha versión.

Finalmente, una vez que la nueva versión **CANARY** ha sido ***VALIDADA*** & estar ***ESTABLE***, se puede redirigir todo el ***TRÁFICO*** hacia dicha versión.

El ***OBJETIVO*** de esta **DEMO#1** es poder **CREAR 2 VERSIONES** de un ***MICROSERVICIO*** & en base a la: **V1** o **V2** poder ***REDIRECCIONAR*** el ***TRÁFICO*** respectivo

por medio de ***REGLAS***.

Se procede a ***VALIDAR*** que los ***MICROSERVICIOS*** estén desplegados.

En este caso se utilizarán los que están desplegados en la ***MODALIDAD***

de: **INTEGRATION-SERVER**, filtrando por: dummy-csm-rest

✓

✓

*dummy-csm-rest-topdown-**01a***

*dummy-csm-rest-topdown-**01b***



<a name="br20"></a> 

Luego, es necesario la creación de los ***RECURSOS***:

✓

✓

✓

✓

***SERVICE:***

*my-istio-service-demo1*

***VIRTUAL-SERVICE:** my-istio-service-demo1*

***DESTINATION-RULE:** my-istio-service-demo1*

***ROUTE:***

*my-istio-service-demo1*

Así mismo, los ***LABELs*** & ***SELECTORs*** manejarán con el mismo

identificador: *my-istio-service-demo1*

Además, el valor del **HOST** es en base al ***PATRÓN***:

*<NombreServicio>.<Namespace>.svc.cluster.local.*

**EXPLICACIÓN:** *“Hay que considerar que el **RECURSO**: **VirtualService***

*está manejando un atributo: (SubSet) que especificará **LA VERSIÓN***

*del **MICROSERVICIO** donde se **REDIRECIONARÁ** el **TRÁFICO***

*&*

*el atributo: (Weight) que especificará el*

***%***

*de **PRIORIDAD***

*que se tendrá para el **REDIRECCIONAMIENTO**”.*

El ***OBJETIVO*** es que se vaya pasando ***PROGRESIVAMENTE***

el **100%** de ***TRÁFICO*** de una: **v1** a **v2**, como se muestra en la ***IMAGEN***

manipulando ***(%)*** & dejando al ***FINAL*** solo la ***VERSIÓN*** estable.



<a name="br21"></a> 

Luego, hay que considerar que los ***LABELs***

a

nivel de

los **INTEGRATION-SERVER** y/o **INTEGRATION-RUNTIME**, manejen

para este escenario los ***LABELs***.

**MICROSERVICIO #1:**

*labels:*

*app: my-istio-service-demo1*

*version: **v1***

**MICROSERVICIO #2:**

*labels:*

*app: my-istio-service-demo1*

*version: **v2***

Luego, se procede a ***VALIDAR*** que todos los ***RECURSOS*** hayan

sido ***CREADOS*** en el ***CLÚSTER***:

*$ oc get **Service,VirtualService,DestinationRule,Route** -n **ace-istio** | grep **my-istio-***

***service-demo1***

Luego, para ***PROBAR*** el **DEMO#1** será necesario obtener la ***URL***

del ***ROUTE*** creado: *my-istio-service-demo1*

En este caso sería: *http://my-istio-service-**demo1**-ace-istio.apps.tarkin.coc-*

*ibm.com*



<a name="br22"></a> 

Luego, la idea es ***PROBAR*** la mayor cantidad de veces

el ***MICROSERVICIO***, en este caso por: **POSTMAN**.

Así mismo, se puede ***PROBAR*** desde línea de comandos,

para enviar ***MASIVAMENTE** (**Secuencial***

*o*

***Paralelamente**)* los

***REQUEST*** por medio de la ***HERRAMIENTA***: **SIEGE**:

*$ siege --concurrent=**20** --reps=**2** -[v*](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)[* ](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)[http://my-istio-service-*](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)[**demo1***](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)[-ace-*](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)*

[*istio.apps.tarkin.coc-ibm.com/*](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)[employeeservice/get/empleados*](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)



<a name="br23"></a> 

Finalmente, se ingresa al ***DASHBOARD*** de **KIALI** & se aprecia

***GRÁFICAMENTE*** como los ***REQUEST*** son redireccionados

***(en VERDE)***, por la ***REGLA*** de ***TRÁFICO*** definida,

a

nivel

de los ***MICROSERVICIOS*** asociados a los: ***INTEGRATION-SERVERs***.

También, este seguimiento de ***REQUEST*** se conecta con el

***DASHBOARD*** de **JAEGER**, como se muestra en ***IMAGEN***.

**B. DEMO #2: (BLUE & GREEN DEPLOYMENT):**



<a name="br24"></a> 

▪

▪

▪

Esta ***ESTRATÉGIA*** maneja **2** entornos de ***PRODUCCIÓN***: **1 ACTIVO (AZUL)** que manejará el ***TRÁFICO*** en vivo & **1 INACTIVO (VERDE)**, donde está desplegada la ***NUEVA***

***VERSIÓN*** de la ***APLICACIÓN***.

Inicialmente, todo el ***TRÁFICO*** es enviado al entorno **(AZUL)**, que es la versión ***ESTABLE*** & cuando la ***NUEVA VERSIÓN*** de la ***APLICACIÓN*** esté desarrollada,

se desplegará en el entorno **(VERDE)** para ***PRUEBAS***, mientras que el entorno **(AZUL)** seguirá procesando el ***TRÁFICO***.

Finalmente, cuando la ***APLICACIÓN*** en el entorno **(VERDE)** haya sido probado al **100%**, el ***TRÁFICO*** es cambiado de forma directa ***(completamente)*** desde el ambiente:

**(AZUL)** a **(VERDE)**.

El ***OBJETIVO*** de esta **DEMO#2** es cambiar el ***TRÁFICO*** manejado entre los entornos ***PRINCIPAL* (AZUL)** & ***CANARY* (VERDE)** progresivamente, hacia una manera ***DIRECTA***

*(el **100%** del **TRÁFICO**).*

Se procede a ***VALIDAR*** que los ***MICROSERVICIOS*** estén desplegados.

En este caso se utilizarán los que están desplegados en la ***MODALIDAD***

de: **INTEGRATION-SERVER**, filtrando por: dummy-csm-rest:

✓

✓

*dummy-csm-rest-topdown-**02a***

*dummy-csm-rest-topdown-**02b***



<a name="br25"></a> 

Luego, es necesario la creación de los ***RECURSOS***:

✓

✓

✓

✓

***SERVICE:***

*my-istio-service-demo2*

***VIRTUAL-SERVICE:** my-istio-service-demo2*

***DESTINATION-RULE:** my-istio-service-demo2*

***ROUTE:***

*my-istio-service-demo2*

Así mismo, los ***LABELs*** & ***SELECTORs*** manejarán con el mismo

identificador: *my-istio-service-demo2*

Además, el valor del **HOST** es en base al ***PATRÓN***:

*<NombreServicio>.<Namespace>.svc.cluster.local.*

**EXPLICACIÓN:** *“Hay que considerar que el **RECURSO**: **VirtualService***

*está manejando un atributo: (SubSet) que especificará **LA VERSIÓN***

*del **MICROSERVICIO** donde se **REDIRECIONARÁ** el **TRÁFICO***

*&*

*el atributo: (Weight) que especificará el*

***%***

*de **PRIORIDAD***

*que se tendrá para el **REDIRECCIONAMIENTO**”.*

El ***OBJETIVO*** en **BLUE & GREEN** es que el **100%** de ***TRÁFICO***

sea aplicado a una ***VERSIÓN***, tal como se muestra en la ***IMAGEN***.



<a name="br26"></a> 

Luego, hay que considerar que los ***LABELs***

a

nivel de

los **INTEGRATION-SERVER** y/o **INTEGRATION-RUNTIME**, manejen

para este escenario el ***LABEL***.

**MICROSERVICIO #1:**

*labels:*

*app: my-istio-service-demo2*

*version: **v1***

**MICROSERVICIO #2:**

*labels:*

*app: my-istio-service-demo2*

*version: **v2***

**IMPORTANTE:** *“La equivalencia en los colores se maneja por la **VERSIÓN**”.*

✓

**V1** => **BLUE**.

✓

**V2** => **GREEN**.

*“Y la que se desee **ACTIVAR,** será la que se deberá **ACTIVAR***

*a nivel del **VIRTUAL-SERVICE**”.*

Luego, se procede a ***VALIDAR*** que todos los ***RECURSOS*** hayan

sido ***CREADOS*** en el ***CLÚSTER***:

*$ oc get **Service,VirtualService,DestinationRule,Route** -n **ace-istio** | grep **my-istio-***

***service-demo2***

Luego, para ***PROBAR*** el **DEMO#2** será necesario obtener la ***URL***

del ***ROUTE*** creado: *my-istio-service-demo2*

En este caso sería: *http://my-istio-service-**demo2**-ace-istio.apps.tarkin.coc-ibm.com*



<a name="br27"></a> 

Luego, la idea es ***PROBAR*** la mayor cantidad de veces

el ***MICROSERVICIO***, en este caso por: **POSTMAN**.

Así mismo, se puede ***PROBAR*** desde línea de comandos,

para enviar ***MASIVAMENTE** (**Secuencial***

***REQUEST*** por la ***HERRAMIENTA***: **SIEGE**:

*o*

***Paralelamente**)* los

*$ siege --concurrent=**20** --reps=**2** -[v*](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)[* ](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)[http://my-istio-service-*](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)[**demo2***](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)[-ace-*](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)*

[*istio.apps.tarkin.coc-ibm.com/*](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)[employeeservice/get/empleados*](http://my-istio-service-demo1-ace-istio.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)



<a name="br28"></a> 

Finalmente, se ingresa al ***DASHBOARD*** de **KIALI** & se aprecia

***GRÁFICAMENTE*** como los ***REQUEST*** son redireccionados

***(en VERDE)***, por la ***REGLA*** de ***TRÁFICO*** definida,

a

nivel

de los ***MICROSERVICIOS*** asociados a los: ***INTEGRATION-SERVERs***.

También, este seguimiento de ***REQUEST*** se conecta con el

***DASHBOARD*** de **JAEGER**, como se muestra en la ***IMAGEN***.



<a name="br29"></a> 

**C. DEMO #3: *(CONTROL DE TRÁFICO: “INGRESS GATEWAY”):***

**ISTIO [proporciona](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer)[ ](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer)[una](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer)[ ](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer)[forma](https://kubernetes.io/docs/concepts/services-networking/ingress/)[ ](https://kubernetes.io/docs/concepts/services-networking/ingress/)**para gestionar el ***TRÁFICO***

que ***INGRESO*** a la ***MALLA*** desde fuera del ***CLUSTER***, por medio

de un recurso de tipo: **GATEWAY *(propio de ISTIO)***.

**IMPORTANTE:** *“NO confundir el recurso: **INGRESS-GATEWAY***

*con el recurso: **GATEWA**, este último se utiliza para configurar*

*el: **INGRESS-GATEWAY**”.*



<a name="br30"></a> 

Se procede a ***VALIDAR*** que los ***MICROSERVICIOS*** estén desplegados.

En este caso se utilizarán los que están desplegados en la ***MODALIDAD***

de: **INTEGRATION-SERVER**, filtrando por: dummy-csm-rest:

✓

*dummy-csm-rest-topdown-**03a***

Luego, es necesario la creación de los ***RECURSOS***:

✓

✓

***GATEWAY:***

***VIRTUAL-SERVICE:** my-istio-service-demo3*

*my-istio-gateway-demo3*

**EXPLICACIÓN:** *“Hay que considerar que el **RECURSO**: **VirtualService***

*hará referencia al **RECURSO**:* **Gateway***, en el cual el **URI** manejando*

*es el del **BackEnd** que expondrá*

*&*

*donde se debe definir*

*la referencia al **SERVICE** de la instancia del **INTEGRATION-SERVER***

*& el **PUERTO** del **ACE12** (7800)*”.



<a name="br31"></a> 

Luego, hay que considerar que los ***LABELs***

a

nivel de

los **INTEGRATION-SERVER** y/o **INTEGRATION-RUNTIME**, manejen

para este escenario los ***LABELs***.

**MICROSERVICIO #1:**

*labels:*

*app: my-istio-service-demo3*

**IMPORTANTE:** *“Para este **DEMO** no se requiere la **VERSIÓN**”.*



<a name="br32"></a> 

Luego, se procede a ***VALIDAR*** que todos los ***RECURSOS*** hayan

sido ***CREADOS*** en el ***CLÚSTER***:

*$ oc get **Gateway,VirtualService** -n **ace-istio** | grep **demo3***

Luego, para ***PROBAR*** el **DEMO#3** será necesario obtener la ***URL***

del

***ROUTE***

generado

automáticamente:

*istio-ingressgateway,*

en el ***NAMESPACE***: istio-system

En este caso sería: [*http://i](http://my-istio-service-demo3-ace-istio.apps.tarkin.coc-ibm.com/)*stio-ingressgateway-istio-system.apps.tarkin.coc-*

*ibm.com*



<a name="br33"></a> 

Luego, la idea es ***PROBAR*** la mayor cantidad de veces

el ***MICROSERVICIO***, en este caso por: **POSTMAN**.

Así mismo, se puede ***PROBAR*** desde línea de comandos,

para enviar ***MASIVAMENTE** (**Secuencial***

*o*

***Paralelamente**)* los

***REQUEST*** por medio de la ***HERRAMIENTA***: **SIEGE**:

*$*

*siege --concurrent=**20** --reps=**2** -v [http://istio-ingressgateway-istio-](http://istio-ingressgateway-istio-system.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)*

[system.apps.tarkin.coc-ibm.com/employeeservice](http://istio-ingressgateway-istio-system.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)[/get/empleados](http://istio-ingressgateway-istio-system.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)



<a name="br34"></a> 

Finalmente, se ingresa al ***DASHBOARD*** de **KIALI**

***GRÁFICAMENTE*** como los ***REQUEST*** son redireccionados

***(en VERDE)***, por la ***REGLA*** de ***TRÁFICO*** definida, nivel

&

se aprecia

a

de los ***MICROSERVICIOS*** asociados a los: ***INTEGRATION-SERVERs***.

También, este seguimiento de ***REQUEST*** se conecta con el

***DASHBOARD*** de **JAEGER**, como se muestra en ***IMAGEN***.



<a name="br35"></a> 

**D. DEMO #4: *(UNIFICANDO TODO):***

Se procede a ***VALIDAR*** que los ***MICROSERVICIOS*** estén desplegados.

En este caso se utilizarán los que están desplegados en la ***MODALIDAD***

de: **INTEGRATION-SERVER**, filtrando por: dummy-csm-rest:

✓

✓

*dummy-csm-rest-topdown-**04a***

*dummy-csm-rest-topdown-**04b***



<a name="br36"></a> 

Luego, es necesario la creación de los ***RECURSOS***:

✓

✓

✓

✓

✓

***GATEWAY:***

***VIRTUAL-SERVICE:** my-istio-service-demo4*

***DESTINATION-RULE:** my-istio-service-demo4*

***SERVICE:***

***ROUTE:***

*my-istio-gateway-demo4*

*my-istio-service-demo4*

*my-istio-service-demo4*

Así mismo, los ***LABELs*** & ***SELECTORs*** manejarán con el mismo

identificador: *my-istio-service-demo4*

Además, el valor del **HOST** es en base al ***PATRÓN***:

*<NombreServicio>.<Namespace>.svc.cluster.local.*

**EXPLICACIÓN:** *“Hay que considerar que el **RECURSO**: **VirtualService***

*está manejando un atributo: (SubSet) que especificará **LA VERSIÓN***

*del **MICROSERVICIO** donde se **REDIRECIONARÁ** el **TRÁFICO***

*&*

*el atributo: (Weight) que especificará el*

***%***

*de **PRIORIDAD***

*que se tendrá para el **REDIRECCIONAMIENTO**”.*



<a name="br37"></a> 



<a name="br38"></a> 

Luego, hay que considerar que los ***LABELs***

a

nivel de

los **INTEGRATION-SERVER** y/o **INTEGRATION-RUNTIME**, manejen

para este escenario el ***LABEL***.

**MICROSERVICIO #1:**

*labels:*

*app: my-istio-service-demo4*

*version: **v1***

**MICROSERVICIO #2:**

*labels:*

*app: my-istio-service-demo4*

*version: **v2***

Luego, se procede a ***VALIDAR*** que todos los ***RECURSOS*** hayan

sido ***CREADOS*** en el ***CLÚSTER***:

*$ oc get **Service,VirtualService,DestinationRule,Route** -n **ace-istio** | grep **my-***

***istio-service-demo4***



<a name="br39"></a> 

Luego, para ***PROBAR*** el **DEMO#4** será necesario obtener la ***URL***

del ***ROUTE*** generado automáticamente: ace-istio-my-istio-gateway-

demo4-xxxx*,* en el ***NAMESPACE***: istio-system

En

este

caso

sería:

[http://ace-istio-my-istio-gateway-demo4-](http://ace-istio-my-istio-gateway-demo4-684888c0ebb17f37-istio-system.apps.tarkin.coc-ibm.com/)

[684888c0ebb17f37-istio-system.apps.tarkin.coc-ibm.com](http://ace-istio-my-istio-gateway-demo4-684888c0ebb17f37-istio-system.apps.tarkin.coc-ibm.com/)



<a name="br40"></a> 

Luego, la idea es ***PROBAR*** la mayor cantidad de veces

el ***MICROSERVICIO***, en este caso por: **POSTMAN**.

Así mismo, se puede ***PROBAR*** desde línea de comandos,

para enviar ***MASIVAMENTE** (**Secuencial***

*o*

***Paralelamente**)* los

***REQUEST*** por medio de la ***HERRAMIENTA***: **SIEGE**:

*$ siege --concurrent=**20** --reps=**2** -v [http://*](http://ace-istio-my-istio-gateway-demo4-684888c0ebb17f37-istio-system.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)[ace-istio-my-istio-gateway-*](http://ace-istio-my-istio-gateway-demo4-684888c0ebb17f37-istio-system.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)*

[*demo4*](http://ace-istio-my-istio-gateway-demo4-684888c0ebb17f37-istio-system.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)[-684888c0ebb17f37-istio-system.apps.tarkin.coc-*](http://ace-istio-my-istio-gateway-demo4-684888c0ebb17f37-istio-system.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)

[*ibm.com/employeeservice/get/empleados*](http://ace-istio-my-istio-gateway-demo4-684888c0ebb17f37-istio-system.apps.tarkin.coc-ibm.com/employeeservice/get/empleados)

**IMPORTANTE:** *“También se podría apuntar **DIRECTAMENTE***

*sin pasar por el **GATEWAY** sino desde el **ROUTE** desde aquí:*

*http://my-istio-service-demo4-ace-istio.apps.tarkin.coc-*

*ibm.com/employeeservice/get/empleados”.*



<a name="br41"></a> 

Finalmente, se ingresa al ***DASHBOARD*** de **KIALI**

***GRÁFICAMENTE*** como los ***REQUEST*** son redireccionados

***(en VERDE)***, por la ***REGLA*** de ***TRÁFICO*** definida, nivel

&

se aprecia

a

de los ***MICROSERVICIOS*** asociados a los: ***INTEGRATION-SERVERs***.

También, este seguimiento de ***REQUEST*** se conecta con el

***DASHBOARD*** de **JAEGER**, como se muestra en ***IMAGEN***.

