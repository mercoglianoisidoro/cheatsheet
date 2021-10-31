---
sort: 1
---

# Base Concepts

## Dependability
the ability to deliver service that can justifiably be trusted.
The alternative definition: the ability to avoid service failures that are more frequent and severe than is acceptable

### Dependability Attributes


- **Availability:**
    - **Definition:** the proportion of time when a system is in a condition that is ready to perform the specified functions.
    - **Measured as:** the percentage of time that a system remains operational.


- **Reliability:**
  - **Definition:** the ability of a system to perform a specified function correctly under the stated conditions for a defined period of time.
  - **Measured as:** the amount of time during which an application operates at a certain level of performance.

- **Safety**:
  - The absence of the risk of endangering human lives and of causing catastrophic consequences to the environment.

- **Integrity:**
  - The absence of unauthorized and incorrect system modifications to its data and system states.

- **Maintainability:**
  - A measure of how easy it is for a system to undergo modifications after its delivery in order to correct faults, prevent problems from causing system failure, improve performance, or adapt to a changed environment.


### Hight Availability
  - **Definition:** The system will continue to function despite the complete failure of any component of the architecture.


## Scalability
The ability of a system to increase resources to accommodate increased demand.

- **Vertical scaling**
  - increase intance size
  - scale up <-> scale down
- **Horizontal scaling**
  - increase intance number
  - scale out <-> scale in



## Metrics for disaster recovery

**Recovery point objective - RPO**: maximum targeted period in which data might be lost ( = data that you can acceptably lose) from an IT service due to a major incident.
If your RPO is defined as 5 minutes, for example, your disaster recovery needs to be designed to restore the data records to within 5 minutes of when the disaster first occurred.

**Recovery time objective - RTO**: actual time it takes to restore a business process or an application back to its defined service level.
If RTO is set to 1 hour, for example, the application should be up and functional within that 1-hour.


An image from [wikipedia](https://fr.wikipedia.org/wiki/Fichier:RTO_RPO.gif)


## Some notes for Disaster Recovery
From :[aws docs](https://aws.amazon.com/blogs/publicsector/rapidly-recover-mission-critical-systems-in-a-disaster/)

**Disaster recovery configurations:**
- **Backup and restore**
- **Pilot Light Solution**: you replicate part of your IT structure for a limited set of core services so that the AWS cloud environment seamlessly takes over in the event of a disaster. Other parts of your infrastructure are switched off and used only during testing.When the time comes for recovery, you can rapidly provision a full-scale production environment around the critical core.
- **Warm standby**: a scaled-down version of a fully functional environment is always running in the cloud.
- **Multi-site**: master master solution