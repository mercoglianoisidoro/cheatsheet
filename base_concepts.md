---
sort: 1
---

# Base Concepts

## Dependability
the ability to deliver service that can justifiably be trusted.
The alternative definition: the ability to avoid service failures that are more frequent and severe than is acceptable

### Dependability Attributes


- **Availability:**
    - **General def:** the proportion of time when a system is in a condition that is ready to perform the specified functions.
    - **Measured as:** the percentage of time that a system remains operational.
- **Hight Availability:**
  - The system will continue to function despite the complete failure of any component of the architecture.

- **Reliability:**
  - **General def:** the ability of a system to perform a specified function correctly under the stated conditions for a defined period of time.
  - **Measured as:** the amount of time during which an application operates at a certain level of performance.

- **Safety**:
  - The absence of the risk of endangering human lives and of causing catastrophic consequences to the environment.

- **Integrity:**
  - The absence of unauthorized and incorrect system modifications to its data and system states.

- **Maintainability:**
  - A measure of how easy it is for a system to undergo modifications after its delivery in order to correct faults, prevent problems from causing system failure, improve performance, or adapt to a changed environment.




## Scalability
The ability of a system to increase resources to accommodate increased demand.

- **Vertical scaling**
  - increase intance size
  - scale up <-> scale down
- **Horizontal scaling**
  - increase intance number
  - scale out <-> scale in