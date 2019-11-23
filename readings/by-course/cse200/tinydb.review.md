# Reading Review 2019-11-22

&middot; by Aldrin Montana

## TinyDB: An Acquisitional Query Processing System for Sensor Networks

##### Main contributions

    This paper details several aspects of acquisitional query processing (ACQP), which addresses
    query processing in an environment with many resource-constrained sensors that are able to
    intelligently, and with precise control, choose when and how data is acquired. I believe the
    main contributions of the paper, given all of the other work in databases for sensors, are the
    proposed query language and programming model extensions. These include clauses that allow
    developers to express intent over sensor parameters and tries to propagate and execute queries
    efficiently using network topology and intelligent exchange of sensor metadata.


##### Methodology
    This paper assumes a hierarchical sensor network and addresses 4 aspects of query processing in
    such an environment: (1) query language, (2) query optimization, (3) query dissemination, or
    query scheduling, and (4) query execution and result gathering. The paper goes into quite a bit
    of detail of each aspect, describing techniques and approaches that are similar to standard
    databases and databases for sensor networks in literature.

    Methods to enable programmers to express query intent over sensor parameters include: how a
    query may execute periodically, event-based triggers for on-demand query execution, aggregation
    over timeframes of sampling, etc. The network topology is leveraged to eliminate propagation of
    queries to sensors that don't need to execute a query, such as if the query is only to be
    executed by the sensor with a specified ID. To support this and other query optimization
    techniques, sensors maintain metadata and periodically exchange with neighbors in the network
    so that multi-query optimization is possible. This paper lists many other methods, but these
    were the most relevant to the database community at the time.


##### Impact of the paper in its field

    There was a time period from the late 80s to approximately 2010 that databases for
    sensor networks were heavily researched. For the amount of time spent on research in this
    field, it is widely considered to be research "before it's time" aka research that was not
    useful until more recently. At the time of TinyDB, sensors were becoming more sophisticated,
    and are now becoming incredibly powerful as many smart phones can be considered sensor devices.
    TinyDB's main impact, in my opinion, is its approach to the "smart sensor" scenario where
    sensors are not merely polling unstructured data. Sensors are capable of providing lots of
    metadata and an interface for acquiring sensor data when queried, and vice versa, was useful.
    
    I'm not sure how much traction this paper had in the field or if it has been considered just
    another paper in the pile of databases for sensors, but with IoT devices potentially becoming
    ubiquitous, I think the described ACQP framework seems to lay a necessary foundation for query
    processing in this environment.


##### Relevance

    My research so far is mostly related to declarative interfaces for storage systems and the use
    of smart storage devices to allow for: better de-coupling between storage and compute and
    minimal movement of data. In this research, primary concerns are bandwidth utilization for
    unnecessary data movement as well as power consumption.

    I think some of the models for calculating power cost of query execution in TinyDB could be
    relevant to our research and perhaps should be something considered in our cost model. While I
    don't imagine too many other direct relationships, something worth exploring may be to take the
    idea of event-based triggers or queries that can invoke operations on the drives themselves in
    order to enable workload simulation on idle disks so that data transformations can happen in
    the background to improve data access for batch workloads.
