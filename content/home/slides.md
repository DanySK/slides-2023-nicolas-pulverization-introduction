+++
weight = 1
+++

# Pulverization

The infrastructures providing networking and computing services are complex, layered and heterogeneous (e.g. edge–fog–cloud interplay).

The implementation of a device's logic should be largely **independent** from the specific application deployment.

The global system behavior of application services gets broken into smaller **computational pieces** that are **continuously** executed across the
available hosts.

{{< multicol >}}
{{% col %}}

A _Logical device_ broken down into five **components**:

- _Behaviour_
- _Communication_
- _State_
- _Sensors_
- _Actuators_

{{% /col %}}

{{< col >}}
{{< figure src="images/futureinternet-12-00203-g002.webp" >}}
{{< /col >}}
{{< /multicol >}}

---

# The Framework

{{< multicol >}}
{{% col %}}
Fundamentals requirements of the framework are:

- **Multiplatform**: the framework should be able to run on any platform (e.g. Android, iOS, Linux, Windows, embedded systems, etc.)
- **Multi-protocol**: the framework should be able to communicate with any protocol (e.g. MQTT, RabbitMQ etc.)
- **Clean API**: the framework should be easy to use and understand

{{% /col %}}

{{< col >}}
{{< figure src="images/Kotlin-logo.png" >}}
{{< /col >}}
{{< /multicol >}}

---

# Framework Architecture

{{< multicol >}}
{{% col %}}
The framework is composed of three main packages:

- **Core**: defines the pulverization concepts and interfaces needed to implement a pulverized system
- **Pulverization platform**: manages all the logic needed to implement a pulverized system
- **RabbitMQ platform**: implementation of a communicator based on RabbitMQ
- **Other communicator** ...
{{% /col %}}

{{< col >}}
{{< mermaid >}}
graph TD
    A[Core] --- B[Pulverization Platform]
    B --- C[RabbitMQ]
    B --- D[...]
{{< /mermaid >}}
{{< /col >}}
{{< /multicol >}}

---

# Configuration DSL

```kotlin
val config = pulverizationConfig {
    logicalDevice("smartphone") {
        BehaviourComponent and CommunicationComponent deployableOn Cloud
        SensorsComponent and ActuatorsComponent deployableOn Device
    }
    logicalDevice("antenna") {
        CommunicationComponent and BehaviourComponent and ActuatorsComponent deployableOn Cloud
    }
}
```

---

# Platform DSL

```kotlin
val platform = pulverizationPlatform(config.getDeviceConfiguration("smartphone")!!) {
        behaviourLogic(SmartphoneBehaviour(), ::smartphoneBehaviourLogic)
        communicationLogic(SmartphoneCommunication(), ::smartphoneCommunicationLogic)
        withPlatform { RabbitmqCommunicator(hostname = "rabbitmq") }
        withRemotePlace { defaultRabbitMQRemotePlace() }
    }

platform.start()
platform.stop()
```

---

# Demo 1

## Single device multiple components

The first demo shows how to "pulverize" a simple device for regulating the moisture of a terrain.
{{< multicol >}}
{{% col %}}

1. The _sensor_ component collect the moisture level of the terrain
2. The _behaviour_ component decides if the _actuator_ component should be activated or not based on the threshold defined in the _state_
3. The _actuator_ component open or close the valve based on the information given by the _behaviour_ component

{{% /col %}}

{{< col >}}
{{< figure src="images/demo-1.svg" width="80%" >}}
{{< /col >}}

{{< /multicol >}}

---

# Demo 2

## Multiple device multiple components

This demo tries to emulate the "hot-warm-cold" game using two smartphone and one antenna.

The two smartphone try to find the antenna measuring the distance between them and the antenna; moreover, the smartphones can communicate with each
other sharing their distance from the antenna.

{{< multicol >}}
{{< col >}}
{{< figure src="images/demo-2-logical-comm.svg" width="55%" >}}
{{< /col >}}

{{< col >}}
{{< figure src="images/demo-2-physical.svg" width="70%" >}}
{{< /col >}}
{{< /multicol >}}

---

# References

- Pulverization framework [https://github.com/nicolasfara/pulverization-framework](https://github.com/nicolasfara/pulverization-framework)
- Demo 1 [https://github.com/nicolasfara/pulverization-moisture-soil](https://github.com/nicolasfara/pulverization-moisture-soil)
- Demo 2 [https://github.com/nicolasfara/pulverization-hot-warm-cold](https://github.com/nicolasfara/pulverization-hot-warm-cold)