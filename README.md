# SKF ADF 

> *SKF is a global supplier of bearings and motion-controlled products and is based in Gothenburg, Sweden. It has geographically expanded to 28 countries with 103 facilities world-wide and 43 thousand employees. SKF is leading a multi-year initiative to transition into data-driven manufacturing and investing into a platform which will spur innovation in factories, simplify the MES (Manufacturing Execution Systems) landscape and significantly reduce cost. At present, each factory has local solutions running on expensive VMWare clusters with no easy way to share data centrally.*

*As part of their World Class Manufacturing 1.0 program, SKF is building a digital platform to run applications in the factories in a "cloud-controlled, locally executed" environment. To enable the edge scenarios specifically, SKF is relying on several technologies from Microsoft which are either in preview or early GA. SKF will like to run machine learning modules and other workloads on top of this platform in the shop floor as part of their multi-year journey (not in scope of current engagement). SKF has successfully conducted multiple POCs to verify the product capabilities. SKF is now looking to mature the platform and bring in production workloads.*

This sample was develop during the [SKF Digital Manufacturing at Edge engagement](https://microsoft.sharepoint.com/:w:/r/teams/CSEHub/ArtifactHub/_layouts/15/Doc.aspx?sourcedoc=%7Be49112d0-405f-4d7a-94fb-55d0288dbd71%7D&action=&cid=2ad4da5d-855a-4a41-ae7a-d10734c6286d) to handle replication of data from Edge to Azure. SKF will have one Edge setup per factory where 3 different sets of database need to be copied to a Azure SQL Database. One of the database will be on SQL Edge the others are on Arc Data Services. This sample contains 5 pipelines which are all clones of the two base types. SKF need different schedule triggers which was easiest to connect to different pipelines. The pipelines differ in some variable values but part from that they are the same. One base type is called the "Initial Setup" which activate Change Tracking on the Source database and create an empty destination database. The other pipeline take care of the data copy activities. The first time executed it will make an full load and after that do incremental copies based on the Change Tracking value. 

## Engagement Links
- [Game plan document](https://microsoft.sharepoint.com/:w:/r/teams/CSEHub/ArtifactHub/_layouts/15/Doc.aspx?sourcedoc=%7Be49112d0-405f-4d7a-94fb-55d0288dbd71%7D&action=&cid=2ad4da5d-855a-4a41-ae7a-d10734c6286d).

## Key Solutions/Topics

## How to run 
1. Deploy the ADF in target Azure Subscription. 
2. Open Linked service object and configure Source and Destination database. 
    2.1 Create IR-Host if needed
3. Run the inital setup pipeline. Which will activate Change Tracking on the source database and create a destination database.
4. Use Visual Data studio or SSMS to look at the result


## Key Technologies
- Azure Data Service.
- SQL Server, SQL Edge, Azure SQL Database