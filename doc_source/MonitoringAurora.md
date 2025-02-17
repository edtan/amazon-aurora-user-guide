# Monitoring an Amazon Aurora DB Cluster<a name="MonitoringAurora"></a>

 This section shows how to monitor Amazon Aurora\. Aurora involves clusters of database servers that are connected in a replication topology\. Monitoring a Aurora cluster typically requires checking the health of multiple DB instances\. The instances might have specialized roles, handling mostly write operations, only read operations, or a combination of both\. You also monitor the overall health of the cluster by measuring the *replication lag*, the amount of time for changes made by one DB instance to be available to the other instances\. 

**Topics**
+ [Overview of Monitoring Amazon RDS](MonitoringOverview.md)
+ [Viewing an Amazon Aurora DB Cluster](Aurora.Viewing.md)
+ [DB Cluster Status](Aurora.Status.md)
+ [DB Instance Status](Overview.DBInstance.Status.md)
+ [Monitoring Amazon Aurora DB Cluster Metrics](Aurora.Monitoring.md)
+ [Enhanced Monitoring](USER_Monitoring.OS.md)
+ [Using Amazon RDS Performance Insights](USER_PerfInsights.md)
+ [Using Amazon Aurora Recommendations](USER_Recommendations.md)
+ [Using Database Activity Streams with Aurora PostgreSQL](DBActivityStreams.md)
+ [Using Amazon RDS Event Notification](USER_Events.md)
+ [Viewing Amazon RDS Events](USER_ListEvents.md)
+ [Amazon RDS Database Log Files](USER_LogAccess.md)
+ [Logging Amazon RDS API Calls with AWS CloudTrail](logging-using-cloudtrail.md)