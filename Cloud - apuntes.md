RESUMEN DE CLOUD COMPUTING Y AWS
================================

Este es mi resumen sobre cloud computing y AWS.


Tabla de contenido:

  * [EL CURSO AWS CLOUD FOUNDATION](#el-curso-aws-cloud-foundation)
  * [CONCEPTOS DE LA NUBE](#conceptos-de-la-nube)
  * [PRECIOS Y FACTURACIÓN](#precios-y-facturación)
  * [INFRAESTRUCTURA GLOBAL](#infraestructura-global)
  * [SEGURIDAD](#seguridad)
  * [REDES](#redes)
  * [CÓMPUTO](#cómputo)
  * [ALMACENAMIENTO](#almacenamiento)
  * [BASES DE DATOS](#bases-de-datos)
  * [LOGS Y MONITORIZACIÓN](#logs-y-monitorización)
  * [ARQUITECTURA](#arquitectura)


---


EL CURSO AWS CLOUD FOUNDATION
-----------------------------

[¿Qué deberías aprender si te vas a dedicar a AWS?](https://roadmap.sh/aws)

En esta introducción los temas que tocaremos serán:

  * Conceptos de la nube: Definir la nube de AWS.

  * Economía y facturación de la nube: Explicar la filosofía de precios de AWS.

  * Infraestructura global de AWS: Identificar los componentes de la infraestructura global de AWS.

  * AWS Cloud security: Describir las medidas de seguridad y cumplimiento de la nube de AWS, incluida AWS Identity and Access Management (IAM).

  * Redes y entrega de contenido: Crear una Amazon Virtual Private Cloud (Amazon VPC).

  * Cómputo: Demostrar cuándo usar Amazon Elastic Compute Cloud (EC2), AWS Lambda y AWS Elastic Beanstalk.

  * Almacenamiento: Diferenciar entre Amazon S3, Amazon EBS, Amazon EFS y Amazon S3 Glacier.

  * Bases de datos: Demostrar cuándo utilizar los servicios de base de datos de AWS, incluidos Amazon Relational Database Service (RDS), Amazon DynamoDB, Amazon Redshift y Amazon Aurora.

  * Arquitectura en la nube: Explicar principios arquitectónicos básicos de la nube de AWS.

  * Escalado y supervisión automáticos: Explorar conceptos clave relacionados con Elastic Load Balancing (ELB), Amazon CloudWatch y Auto Scaling.

Documentación: <https://docs.aws.amazon.com/>


---


CONCEPTOS DE LA NUBE
--------------------

Cloud Computing es la entrega instantánea bajo demanda de potencia de cómputo, bases de datos, almacenamiento, aplicaciones y otros recursos de TI a través de Internet con un sistema de precios de pago por uso. Estos recursos se ejecutan en equipos de servidores ubicados en grandes centros de datos en diferentes partes del mundo.

IaaS: te ofrece instancias de cómputo, donde tu instalaras y administrarás el sistema operativo. Un ejemplo de Iaas es contratar máquinas virtuales en la nube. El proveedor se responsabiliza del hardware y tu del sistema operativo, aplicaciones y datos.

PaaS: te ofrece un sistema sobre el cual tu instalas tus aplicaciones. Un ejemplo de PaaS es contratar hosting web. El proveedor se responsabiliza del hardware y del s.o., y tu de las aplicaciones y datos.

SaaS: te ofrece una aplicación. Un ejemplo de SaaS es GMail. El proveedor se responsabiliza del hardware, del s.o. y de la aplicación, y tu de los datos.

Aquí tienes los [nombres de los servicios en cada Cloud](https://comparecloud.in/) y aquí otra [comparación de los servicios en cada Cloud](https://cloudcomparisontool.com/).

Y aquí tienes los [servicios de AWS clasificados](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/amazon-web-services-cloud-platform.html). De ellos veremos:

  * Amazon EC2 (Elastic Cloud Compute)
  * Amazon VPC (Virtual Private Cloud)
  * Amazon Route 53 (DNS)
  * Amazon Auto-Scaling & ELB (Elastic Load Balancer)
  * Amazon EBS (Elastic Block Store)
  * Amazon EFS (Elastic File System)
  * Amazon S3 (Simple Storage Service) & S3 Glacier
  * Amazon RDS (Relational Database Services) -> AWS Aurora
  * Dynamo DB (No-Relational Database Services)
  * Amazon ECS & Fargate & ERS (Containers)
  * Amazon EKS (Kubernetes)
  * Amazon Lambda (Serverless Computing)
  * Amazon Elastic Beanstalk
  * Amazon CloudFront (Content Delivery Network)
  * Amazon IAM (Identity and Access Management)
  * AWS Key Management Service (KMS)
  * Amazon SNS (Simple Notification Service)
  * Amazon SQS (Simple Queue Service)

Para interaccionr con la nube podemos usar la consola Web, CLI, SDK, API, e IaaC.



### Interaccionar con la cónsola

Por ejemplo, acceder a las instancias de AWS tienes la URL <https://console.aws.amazon.com/ec2/#Instances>



### Interaccionar con CLI

Debes instalar una línea de comandos especial que permite ejecutar instrucciones para interactuar con los servicios de tu cloud. 

    $ aws <servicio> <comando> [parámetros]

Por ejemplo:

```
$ export AWS_ACCESS_KEY_ID=XXXXXXXXX
$ export AWS_SECRET_ACCESS_KEY=xxxxxxxxxx
$ export AWS_DEFAULT_REGION=us-east-1
$ aws ec2 describe-instances
$ aws ec2 run-instances --image-id ami-0c101f26f147fa7fd --instance-type t2.micro
$ aws ec2 describe-instances
$ aws ec2 terminate-instances --instance-id i-xxxxxxxxxxxxxxxxx
```

En el caso de AWS encuentras una [introducción](https://docs.aws.amazon.com/cli/latest/userguide/) y una [referencia](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/) en <https://docs.aws.amazon.com/cli/>



### Interaccionar con SDK

Las diferentes tecnologías de Cloud ofrecen librerías para que nuestros programas puedan acceder a los servicios que ofrecen. Dichas librerías están disponibles para los lenguajes de programación más populares: Java, Python, Ruby, Go, .Net, etc.

Por ejemplo:

```
class EC2InstanceWrapper:

    def __init__(
        self, ec2_client: Any, instances: Optional[List[Dict[str, Any]]] = None
    ) -> None:
        self.ec2_client = ec2_client
        self.instances = instances or []

    @classmethod
    def from_client(cls) -> "EC2InstanceWrapper":
        ec2_client = boto3.client("ec2")
        return cls(ec2_client)

    def start(self) -> Optional[Dict[str, Any]]:
        if not self.instances:
            logger.info("No instances to start.")
            return None

        instance_ids = [instance["InstanceId"] for instance in self.instances]
        try:
            start_response = self.ec2_client.start_instances(InstanceIds=instance_ids)
            waiter = self.ec2_client.get_waiter("instance_running")
            waiter.wait(InstanceIds=instance_ids)
            return start_response
        except ClientError as err:
            logger.error(
                f"Failed to start instance(s): {','.join(map(str, instance_ids))}"
            )
            error_code = err.response["Error"]["Code"]
            if error_code == "IncorrectInstanceState":
                logger.error(
                    "Couldn't start instance(s) because they are in an incorrect state. "
                    "Ensure the instances are in a stopped state before starting them."
                )
            raise
```

En el caso de AWS encuentras una [referencia](https://docs.aws.amazon.com/sdkref/latest/guide/overview.html) y [ejemplos](https://github.com/awsdocs/aws-doc-sdk-examples?tab=readme-ov-file) en <https://docs.aws.amazon.com/cli/>



### Interaccionar con API

Todas las tecnologías de Cloud ofrecen una API REST, de más bajo nivel que su CLI y que su SDK, que también permite operar con los servicios que ofrecen.



### Interaccionar con IaC

IaC is an approach to provision and manage resources such as cloud infrastructure. It allows you to define and deploy your resources using human-readable definition files or code. IaC allows a declarative specification of the desired infrastructure state. This means compute instances, storage, networking, security groups, and other cloud services.

**Ejemplo con AWS CloudFormation**

```
Resources:
  NewEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: "ami-0c101f26f147fa7fd"
      InstanceType: "t2.micro"
```

<https://us-east-1.console.aws.amazon.com/cloudformation> -> Create stack -> Upload a template file -> choose your previously created "ec2.yml" -> Next -> Give the stack a name -> Skip through all the options and deploy the stack -> status CREATE_COMPLETE


**Ejemplo con Terraform**

<https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli>


```
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "ec2" {
  ami = "ami-0c101f26f147fa7fd"
  instance_type = "t2.micro"
  count = 100
}

$ terraform init
$ terraform apply
$ terraform destroy
```


---


PRECIOS Y FACTURACIÓN
---------------------

Por favor, lee: <https://docs.aws.amazon.com/whitepapers/latest/how-aws-pricing-works/>

Se cobra por cómputo (por hora, y en Linux por segundo), por almacenamiento (por Gb), y por tráfico de salida (por Gb):

La [calculadora de precios](https://calculator.aws/#/) permite hacer estimaciones de cuánto te costará un producto.

El panel de facturación de AWS te permite ver el estado de tus gastos de AWS del mes hasta la fecha, e identificar los servicios que forman parte de dichos gastos.


---


INFRAESTRUCTURA GLOBAL
----------------------

Por favor, lee: <https://aws.amazon.com/about-aws/global-infrastructure/>

Una región AWS es un área geográfica localizada dentro de un país.

Cada región está formada por tres o más zonas de disponibilidad, que son lugares físicamente aislados dentro de dicha región.

Cada zona de disponibilidad está formada por uno o varios datacenters, y están conectadas con el resto de zonas de disponibilidad de su región con enlaces de fibra de baja latencia. 

Además de las regiones, AWS también tiene los llamados puntos de presencia, que son una especie de cachés de contenido o proveedores de contenido.

Imagina, por ejemplo, que España sea una región AWS, y que disponga de datacenters en tres zonas de disponibilidad cerca de las regiones con más empresas: Barcelona, Madrid y Valencia. Pero que además disponga puntos de presencia cerca de otras ciudades importantes del estado, para acercar el contenido a los clientes y disminuir así la latencia.


---


SEGURIDAD
---------

Design principles for security pillar

  * Implement a strong identity foundation (IAM , autentificación y autorización):

    Implement the principle of least privilege and enforce separation of duties with appropriate authorization for each interaction with your AWS resources. Centralize identity management, and aim to eliminate reliance on long-term, static credentials.

  * Protect data in transit and at rest (KMS , encriptación):

    Classify your data into sensitivity levels, and use mechanisms, such as encryption, tokenization, and access control, where appropriate.

  * Maintain traceability (CloudTrail, logs):

    Monitor, alert, and audit actions and changes to your environment in real time. Integrate log and metric collection with systems to automatically investigate and take action.

  * Apply security at all layers:

    Apply a defense-in-depth approach with multiple security controls, and apply it to all layers (for example, edge of network, virtual private cloud [VPC], load balancing, every instance and compute service, operating system, application, and code).

  * Keep people away from data:

    Use mechanisms and tools to reduce or eliminate the need for direct access or manual processing of data. This reduces the risk of mishandling or modification and human error when handling sensitive data.

  * Prepare for security events:

    Prepare for an incident by having incident management and investigation policies and processes that align to your organizational requirements. Run incident response simulations, and use tools with automation to increase your speed for detection, investigation, and recovery.

  * Automate security best practices:
  
    Automated software-based security mechanisms improve your ability to securely scale more rapidly and cost-effectively. Create secure architectures, which includes implementing controls that are defined and managed as code in version-controlled templates.

Con las políticas de IAM, puede permitir o denegar el acceso a servicios de AWS (como Amazon S3), recursos individuales de AWS (como un bucket de S3 específico) o acciones individuales de API (como s3:CreateBucket). Una política de IAM solo puede aplicarse a usuarios, grupos, o roles de IAM, y no podrá restringir al administrador de AWS.

[Simulador de políticas IAM](https://policysim.aws.amazon.com/)

AWS Organizations es una especie de Directorio, con la raíz, las Unidades Organizativas (OU), y las cuentas. Permite aplicar políticas a las OU y a las cuentas. También permite agrupar cuentas y aplicar políticas a dichos grupos. Además, también permite facturación unificada.

Pero además, con AWS Organizations, se pueden utilizar políticas de control de servicios (SCP) para establecer unos permisos máximos para los usuarios de la organización.


---


REDES
-----

En este punto comentaré las herramientas de Cloud que tiene que ver con redes: creación de redes virtuales para disponer en ellas máquinas virtuales y bases de datos; servicio de DNS; y puntos de presencia ("COntent Delivery Netwoks"). Los nombres corresponden a los que reciben en AWS.



### Redes virtuales

Por favor, lee: <https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html>

Una red virtual (VPC) emula una red física, utilizando componentes de red *definidos por software*: switches, routers, cortafuegos, y balanceadores de carga.

Cuando creamos una nueva VPC, ésta ocupa toda la región en la que trabajamos, y tiene asignado un rango de IPs privadas.

Cuando creamos una subred dentro de la VPC, dicha subred ocupa una zona de disponibilidad dentro de la región, y dispone de un subrango de IPs dentro del rango de IPs de la VPC.

En dichas redes y subredes virtuales podemos alojar instancias de cómputo EC2 y bases de datos RDS. Otros servicios, como por ejemplo Lambda o S3, son externos a las VPC.

Toda VPC y toda subred automáticamente tienen una tabla de enrutamiento asociada.

En Amazon cada rango de IPs tiene cinco IPs reservadas, en lugar de dos:
  * La primera (.0) es la dirección de la red.
  * La segunda (.1) es la dirección del router local.
  * La tercera (.2) es la dirección del DNS.
  * La cuarta (.3))está reservada para algún uso futuro.
  * La última (.255) es la dirección de broadcast.

Una manera de hacer accesibles des del exterior las instancias de cómputo EC2 (máquinas virtuales) en la VPC es colocarlas en una subred pública, esto es, una subred que además de tener IPs privadas tiene también IPs públicas y a la que se asocia una puerta de enlace a internet ("Internet gateway"). Una vez tenemos puerta de enlace, podemos añadir una entrada a la tabla de enrutamiento de la VPC que diga que todo el tráfico no local (0.0.0.0/0) lo redirija al Internet Gateway, que hará NAT para las instancias.

Una manera de hacer que una instancia tenga una IP pública estática que no cambie, es asignarle una IP elástica. La IP elástica se puede reasignar a otra instancia o a un balanceador de carga.

Los recursos que no queremos que sean accesibles des de Internet los podemos colocar en una subred privada. En la subred privada sólo tendrán IP privada, y tampoco los recursos podrán acceer a Internet a menos que asociemos dicha subred a un NAT Gateway e incorporemos una regla a la tabla de enrutamimiento de la subred.

En las VPC tenemos dos tipos de cortafuegos: cortafuegos de red ("Acces Control Lists") que protegen una subred, y cortafuegos personales ("Security Groups") que protegen una instancia de cómputo EC2.

Los Security Groups:

  * Son cortafuegos con estado: si con una regla permito un determinado tráfico de petición hacia un servicio, automáticamente también estoy permitiendo el tráfico de respuesta a dicha petición.

  * La política por defecto es restrictiva así que las reglas que añadimos son para permitir tráfico. Si el paquete no encaja con ninguna regla, se deniega.

  * Cuando creamos un security group, por defecto no tiene ninguna regla que permita el tráfico de entrada, y tiene una regla que permite todo el tráfico de salida. Así que por defecto todo el tráfico de entrada está bloqueado y todo el tráfico de salida está permitido.

Los Access Control List:

  * Son cortafuegos sin estado: si con una regla permito un determinado tráfico de petición hacia un servicio, también debo crear otra regla que permita el tráfico de respuesta a dicha petición, o si no la respuesta quedará bloqueada.

  * Podemos añadir reglas tanto para permitir como para denegar tráfico de entrada y de salida.

  * Toda VPC y toda subred tiene asociado un ACL. Un ACL por defecto tiene dos reglas para tráfico de entrada y dos reglas para tráfico de salida: una regla con número de orden 100 que permite tráfico a todos los puertos, y una regla * que deniega todo el tráfico que no encaje con reglas anteriores. Así que por defecto todo el tráfico de entrada y salida está permitido.

Existe un servicio más avanzado de cortafuegos de red, de pago, que también detecta y previene intrusiones, llamado [AWS Network Firewall](https://docs.aws.amazon.com/network-firewall/latest/developerguide/what-is-aws-network-firewall.html).

¿Cómo implementaría un montaje como éste, con un servidor web en una subred pública y otro servidor web en una subred privada?

![](Cloud.red1.png)

 01. Crear VPC: rango 10.0.0.0/16 , activar "DNS hostnames"

 02. Añadir Internet Gateway (IG): adjuntar a VPC

 03. Crear Subnet 1: adjuntar a VPC , rango 10.0.0.0/24 , activar "auto-assign public IPv4 address"

 04. Crear Subnet 2: adjuntar a VPC , rango 10.0.2.0/23

 05. Añadir NAT Gateway: adjuntar a VPC - Subnet 1 , asignar Elastic IP

 06. Añadir Route Table 1: adjuntar a VPC - Subnet 1 , existe regla 10.0.0.0/16 -> local , añadir regla 0.0.0.0.0/0 -> IG

 07. Route Table 2: adjuntar a VPC - Subnet 2 , existe regla 10.0.0.0/16 -> local , añadir regla 0.0.0.0.0/0 -> Nat Gateway

 08. Security Group 1: adjuntar a VPC , añadir regla aceptar "Inbound HTTP from Anywhere-IPv4" , adjuntar a la instancia EC2 en Subnet 1

 09. Security Group 2: adjuntar a VPC , añadir regla aceptar "Inbound HTTP from Security Group 1" , adjuntar a la instancia EC2 en Subnet 2

¿Cómo puedo acceder a un servicio externo a la VPC, por ejemplo a un bucket S3 o una tabla DynamoDB, des de una instancia de cómputo EC2 que se encuentra dentro de una VPC? Aunque no existe una conectividad directa entre ellos, la instancia EC2 puede acceder a los servicios externos a la VPC a través de la URL de éstos. Pero entonces la petición será enrutada a través de Internet, incurriendo en costes de transferencia de datos y baja velocidad. Para solucionar este problema podemos crear una conexión directa entre servicios internos y externos a la VPC utilizando [Interface VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html) y [Gateway VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/gateway-endpoints.html), que aquí no explicaré.



### DNS

Todos los proveedores de cloud ofrecen servicio de DNS. En Amazon este servicio se llama [Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/). DNS nos permite acceder a los servicios e instancias mediante la URL que el proveedor de cloud les otorga.

En el caso que hayamos comprado un dominio a través de un registrador, podemos gestionar los nombres de nuestros servicios AWS en el DNS del registrador, pero también podemos hacer que el DNS del registrador delegue y apunte al servidor DNS del proveedor de cloud y manejar más cómodamente ahí nuestros registros DNS.

En un DNS normal estamos acostumbrados a usar registros que asocian un nombre a una IP única:

    nombre    A    IP

Sin embargo, en Route 53 y otros DNS de proveedores cloud, un nombre puede tener asociadas varias IPs diferentes con réplicas del mismo servicio:

    nombre    A    IP_1
    nombre    A    IP_2
    nombre    A    IP_3

De tal manera que cuando un cliente pregunta al DNS por el nombre de una máquina para saber su IP se pueden hacer cosas como:

  * "Balancear la carga", y que a cada pregunta por el mismo nombre el DNS de una IP diferente.

  * En caso de servicios replicados en diferentes regiones, dar la IP con geolocalización más cercana al cliente, o con menor latencia.

  * Monitorizar una IP y si su servicio asociado "cae", resolver a otra de las IPs de la lista.



### Puntos de presencia

Los puntos de presencia, fuera de los centros de cálculo de las regiones, actúan a modo de [proxy-caché](https://es.wikipedia.org/wiki/Servidor_proxy#Proxy_cach%C3%A9) o de [red de distribución de contenidos](https://es.wikipedia.org/wiki/Red_de_distribuci%C3%B3n_de_contenidos), acercando la información a los clientes. En Amazon este servicio se llama [CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/).

Si contratamos CloudFront nuestros clientes ganarán en velocidad de acceso a los datos pero nosotros pagaremos por transferencia de datos y por peticiones HTTPS. Sin embargo, con CloudFront podemos hacer cosas como asociar una URL a una página web almacenada en un bucket S3, y entonces servir dicha web sin necesidad de una instancia EC2 con un servidor web.


---


CÓMPUTO
-------

En este punto comentaré las herramientas de Cloud que permiten ejecutar aplicaciones: máquinas virtuales; contenedores; servidores de aplicaciones; y lo que ha venido a llamarse "serverless computing". Los nombres corresponden a los que reciben en AWS.



### Máquinas virtuales

EC2 , LOAD BALANCING Y ESCALADO

Por favor, lee: <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html>



### Contenedores

ECS , FARGATE, PRIVATE REGISTRY Y KUBERNETES



### Servidor de aplicaciones web

BEANSTALK



### Serverless computing

LAMBDA


---


ALMACENAMIENTO
--------------

Por favor, lee: <https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html>


---


BASES DE DATOS
--------------

Por favor, lee: <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html>


---


LOGS Y MONITORIZACIÓN
---------------------

  * CloudTrail: registra acciones que se realizaron desde cónsola web, cli, o SDK (llamadas a la API, eventos de seguridad)

  * CloudWatch: registra métricas, uso de recursos

  * Logs de servicios: logs de S3, flujos de VPC, solicitudes al ELB

  * Trusted Advisor: recomendaciones sobre optimización de costos, seguridad, tolerancia a errores, límites de servicio y mejora del rendimiento

  * Config: audita y evalúa las configuraciones de los recursos de AWS, mantiene un histórico de cambios

  * EventBridge: bus de eventos, conecta aplicaciones a eventos

  * Inspector: escanea vulnerabilidades en instancias EC2 y contenedores ECR

  * Security Hub: comprobaciones continuas, alertas y recomendaciones de seguridad (¿IDS basado en firmas?)

  * GuardDuty: identifica acciones inesperadas, no autorizadas y malintencionadas (¿IDS basado en anomalías?)

  * Shield: protege la red contra ataques DDOS

  * WA (well-architected): helps you review the state of your workloads and compare them to the latest AWS architectural best practices


---


ARQUITECTURA
------------

The AWS Well Architected Framework is a guide that documents best practices, and is organized into six pillars:

  * Operational Excellence: Adresses the ability to run systems and gain insight into their operations to deliver business value. It also addresses the ability to continuously improve supporting processes and procedures.

  * Security: Adresses the ability to protect information, systems, and assets while delivering business value through risk assessments and mitigation strategies.

  * Reliability: Adresses the ability of a system to recover from infrastructure or service disruptions and dynamically acquire computing resources to meet demand. It also addresses the ability of a system to mitigate disruptions, such as misconfigurations or transient network issues. 

  * Performance Efficiency: Adresses to maximize the performance by using computation resources efficiently, and to maintain that efficiency as the demand changes.

  * Cost Optimization: Adresses to reduce costs.

  * Sustainability: addresses the ability to build architectures that maximize efficiency and reduce waste.

As you design a solution, think carefully about trade offs so you can select an optimal approach: trade consistency, durability, and space for time and latency to deliver higher performance.

*Implement scalability. Ensure that your architecture can handle changes in demand.*

*Automate the provisioning, termination, and configuration of resources.*

*Provision your computing infrastructure using code instead of manual processes.*

*Treat resources as disposable, taking advantage of the dynamically provisioned nature of cloud computing.*

*Use loosely coupled components, designing architectures with independent components.*

*Design services, not servers.*

*Choose the right database solution. Match technology to the workload, not the other way around.*

*Avoid single points of failure. Assume everything fails. Then, design backward.*

*Optimizing for cost.*

*Use caching to minimize redundant data retrieval operations, improving performance and cost.*

*Build security into every layer of your infrastructure.*

Aquí tienes una lista de [buenas prácticas con AWS](https://roadmap.sh/best-practices/aws)

Estudia ejemplos de [patrones de arquitecturas AWS](https://aws.amazon.com/architecture/)

Para realizar tus diagramas de arquitecturas AWS dispones de este juego de [iconos AWS](https://aws.amazon.com/architecture/icons/)
