[https://kdakan.github.io/Azure-Architecture-Tutorial/](https://kdakan.github.io/Azure-Architecture-Tutorial/)

# AZURE ARCHITECTURE TUTORIAL

## Table of contents
* [ 1. AZURE CLOUD](#1-azure-cloud)
* [ 1.1. Cloud terms](#11-cloud-terms)
* [ 2. IAAS (INFRASTRUCTURE AS A SERVICE)](#2-iaas-infrastructure-as-a-service)
* [ 2.1. Iaas Compute services](#21-iaas-compute-services)
* [ 2.2. Iaas Storage services](#22-iaas-storage-services)
* [ 2.3. Iaas Virtual networking services](#23-iaas-virtual-networking-services)
* [ 3. PAAS (PLATFORM AS A SERVICE)](#3-paas-platform-as-a-service)
* [ 3.1. Paas Compute services](#31-paas-compute-services)
* [ 3.2. Paas Data services](#32-paas-data-services)
* [ 3.3. Paas Enterprise Integration services](#33-paas-enterprise-integration-services)
* [ 3.4. Paas Content & Media services](#34-paas-content--media-services)
* [ 3.5. Paas Developer services](#35-paas-developer-services)
* [ 3.6. Paas Analytics & IOT services](36-#paas-analytics--iot-services)
* [ 3.7. Paas Security services](#37-paas-security-services)
* [ 4. HIGH AVAILABILITY & SCALABILITY](#4-high-availability--scalability)
* [ 4.1. CAP theorem](#41-cap-theorem)
* [ 4.2. Redundancy](#42-redundancy)
* [ 4.3. Handling failures](43-#handling-failures)
* [ 4.3.1. Graceful degradation example](#431-graceful-degradation-example)
* [ 4.4. Deployment automation](#44-deployment-automation)
* [ 4.5. Scalability](#45-scalability)
* [ 4.6. Using Azure CDN](#46-using-azure-cdn)
* [ 4.7. Data partitioning strategies](#47-data-partitioning-strategies)
* [ 4.8. Load performance testing](#48-load-performance-testing)
* [ 4.9. Testing in production (Canary release)](#49-testing-in-production-canary-release)
* [ 5. CONTINUOUS DELIVERY (CI/CD) ON VSTS](#5-continuous-delivery-cicd-on-vsts)
* [ 6. DOCKER CONTAINERS (INTRO)](#6-docker-containers-intro)
* [ 6.1. Docker terms](#61-docker-terms)
* [ 6.2. Docker CLI commands](#62-docker-cli-commands)
* [ 7. CLOUD IDENTITY WITH AZURE AD](#7-cloud-identity-with-azure-ad)
* [ 7.1. Web app authentication flow](#71-web-app-authentication-flow)
* [ 7.2. Web API authentication using OAuth and Open ID Connect on Azure AD](#72-web-api-authentication-using-oauth-and-open-id-connect-on-azure-ad)
* [ 7.3. Web API authentication flow when called from a web app](#73-web-api-authentication-flow-when-called-from-a-web-app)

## 1. AZURE CLOUD
* Azure cloud offers language agnostic infrastructure services, platform services, security & management
* Azure data centers are called regions
* Service names and options on the Azure portal which contain "classic", are usually legacy and not recommended
* We create a resource in a region, under a resource group, and with an Azure subscription
* Resource groups can be handy to replicate a project environment, and Azure subscriptions help in reporting and managing costs
* We can either use the Azure portal manually, or use the Azure command line interface `az` for automation (we need to type both resource group and resource name to identify a resource)

### 1.1. Cloud terms:
* Iaas (Infrastructure as a service)
* Paas (Platform as a service)
* Aaas (Application as a service)
* API (Web services)

## 2. IAAS (INFRASTRUCTURE AS A SERVICE):
We need to patch/upgrade the operating system, install, maintain, and upgrade our software

### 2.1. Iaas Compute services: 
* Virtual machines (Windows, Linux, Containers, we can also select images with pre-installed software like web servers or databases) 
* VMs inside the same availability set won't be patched/upgraded/restarted all at the same time and don't share same power supply or network switch)
* When we create a VM resource, Azure automatically creates other resources like storage account, network interface, network security group (firewall), virtual network, and public IP address all related to this VM
* We can always change VM specs like number of cores, memory and add more disk, where Azure will keep the data on the VM and just migrate it to a new VM
* We can also login to the VM with user/password, or create an SSH key pair with ssh-keygen on Linux/MacOS, connect using SSH (secure shell), stop and restart the VM
* When we stop a VM on the Azure portal, we only pay for the storage, note that this corresponds to deallocate command on azure CLI rather than stop command)

### 2.2. Iaas Storage services:
* BLOB storage (offers hot access, meaning frequent, cool, and archive access tiers, can replicate to another region for disaster recovery, can grant access to specific data/files for a limited time, can host files for transfer to an Azure CDN)
* Table storage (for key-value pairs)
* File storage
* Queue storage (offers basic HTTP interface for using queues)

### 2.3. Iaas Virtual networking services:
* Virtual network (can limit inbound and outbound traffic and ports using security group, can also divide the virtual network into subnets for security)
* Load balancer (can be used outside a virtual network, or inside a virtual network as an internal load balancer to route to subnets)
* DNS
* Express route
* Traffic manager (works at DNS level, can route requests from a specific region to that region, can also do load balancing to route requests to the lowest latency region, can also do disaster-recovery type request routing with active-standby regions)
* VPN gateway (can connect a virtual network with a local resource)
* Application gateway

## 3. PAAS (PLATFORM AS A SERVICE):
Easy setup and maintenance, cannot RDP onto the machine, but can do remote debugging with visual studio

### 3.1. Paas Compute services:
* App service plan (we choose the number of cores, hard disk capacity, RAM memory, number of slots, backup plan, region, etc. similar to a VM, and we attach one or more app services to this app service plan, allows scale up and scale out)
* App service (web apps, rest APIs, allows instant deployment without downtime by swapping between slots like test, staging or production)
* App service (mobile apps, allows users to work offline and later synchronize when online)
* App service (web app for containers or Azure Kubernetes service, allows hosting docker images from docker hub or Azure container registry - a private docker hub, which is created from VSTS build)
* We can choose to deploy from FTP, Dropbox, OneDrive, VSTS, Github, BitBucket, or local Git repository
* If we choose a local Git repository, we can upload our source to azure with: `git init`, `git add .`, `git commit`, `git add remote azureprod https://(remote repo URL from Azure portal)`, `git push azureprod master`, after this, Azure automatically determines the type of the uploaded code and builds the project, runs npm install and npm run to install dependencies and run the project
* App service has deployment slots, which allows us to test code before deploying to production, and also revert back to the previous code
* Each deployment slot has its own different app settings (environment variables), endpoint URLs, and git repository addresses
* We can also monitor and define alerts (like email or webhook post) for thresholds on selected metrics (like number of requests, errors, CPU usage, memory usage, etc.) in some period (last X minutes, hours, days, etc.) on selected resources
* We can ssh or bash onto the server or the container for an app service, under developer tools/advanced tools/go menu (kudo) to see the files and edit them for troubleshooting
* API manager (can throttle and authenticate requests, provides self-service developer portal for docs and access key subscriptions for 3rd party clients)
* Logic apps
* Notification hubs (push a large number of notifications)
* (With app service plan, we can vertically scale up or horizontally scale out)
* Service fabric (microservices, can be stateless or stateful, hosted in a cluster of machines, can be used behind a load balancer)
* Azure functions (we can create them under function apps menu, offers serverless deployment, we pay only for the time the function is executed, can be triggered from an HTTP request, timer, queue, service bus, blob storage, cosmos db, event hub, IoT, Github, webhooks, etc.)
* Batch service
* Cloud services (legacy, do not use, somewhere between app services and virtual machines)

### 3.2. Paas Data services:
* SQL database (offers SQL databases inside a logical server, different databases inside the same server does not mean they share the same physical machine, can do geo-replication between regions)
* Redis cache
* Cosmos DB (key-value pairs, documents or graph data, offers transaction and a MongoDB compatible API, can do geo-replication between regions but the secondary region will be read-only)
* SQL data warehouse
* Azure search (can do full-text search on CosmosDB and Azure SQL by crawling data, offers search suggestions, scoring, and supports most languages)

### 3.3. Paas Enterprise Integration services:
* Service bus (offers basic queues with a single receiver and also pub/sup topics with multiple receivers)
* Biztalk services
* Logic apps

### 3.4. Paas Content & Media services:
* CDN (Content delivery network
* Media services

### 3.5. Paas Developer services:
* VS team services
* Azure SDK
* Application Insights

### 3.6. Paas Analytics & IOT services:
* HDInsight (Hadoop clusters, can do distributed big data analysis with map reduce)
* Machine learning
* Data factory (move a large amount of data)
* Event hubs (can handle millions of events received from IoT devices, can pass them to Stream analytics)
* Stream analytics

### 3.7. Paas Security services:
* Portal
* Azure active directory (we can synchronize local active directory with Azure AD to authenticate internal users)
* Multi-factor authentication
* OAuth
* OpenID Connect
* Key vault (can protect secret keys and monitor who is using them)
* Azure AD B2C (we can also use asp.net identity framework, or identity server with ASP.NET applications)

## 4. HIGH AVAILABILITY & SCALABILITY:
### 4.1. CAP theorem:
* CAP = Consistency, Availability, (network) Partition tolerance
* Consistency means that we always read what is most recently written, unlike in eventually consistent systems where we may sometimes read stale (old) data
* Availability means every request gets a non-error response, unlike in highly available systems we have 9x.xx % SLA's and we should handle connection errors by repeat logic
* (Network) Partition tolerance means the system works even if some of its parts are disconnected from the rest of the system
* A distributed system can always have network failures, and so it cannot be CAP = consistent, available, and (network) partition tolerant all three at the same time, we should either choose CA, AP, or CP for the case when a network error occurs
* We don't have to choose the same CA, AP, or CP for all parts of the system, we can combine different parts with different choices
* Social network systems require availability, financial systems require consistency
* CA is not favored in cloud systems, because the best way for scaling is network partitioning (horizontal scaling)
* Relational databases implement CA for ACID transactions, and are not very scalable, but they also have replication feature with read-only partitions
* Most scalable systems favor either CP (and try to cover A by implementing retry logic), or AP (and try to cover C with eventual consistency)

### 4.2. Redundancy:
* We make replicate resources and make them redundant to prevent a single point of failure 
* Data replication does not mean data backup/restore solution, data can be updated/deleted accidentally so we still need a backup/restore solution
* Storage (has replication enabled by default)
* Virtual machines (we can use availability set) 
* App service plan (we can use scale-out options with multiple instances, and also configure auto-scaling here, can even host instances in different regions and use traffic manager for geo-scaling)
* Function app (serverless Azure functions are always available)
* Cosmos DB (we can do geo-scaling)
* Availability SLA's are different for different parts of the system and for different tiers (there is no availability guarantee for free and shared instances)
* 99% availability means 7.2 hours downtime per month
* 99.9% availability means 43.2 minutes downtime per month
* 99.95% availability means 21.6 minutes downtime per month
* 99.99% availability means 4.32 minutes downtime per month
* Availability figure of a system equals to all availability figures of its parts multiplied

### 4.3. Handling failures:
* Failure is inevitable and is expectedto happen any time in cloud systems commodity harware, so we can use retry logic with exponential time (like 4 sec, 8 sec, 16 sec) in case of connection errors (HTTP 500s and 408)
* Retry logic comes default with many Azure services, Azure retry guidance page lists all Azure services different default retry logic settings (how many times, period, etc.)
* Azure storage, Azure service bus, CosmosDB, and Azure Redis cache implement configurable retry logic in their SDK's, Azure SQL retry logic is implemented in entity framework, but not in ADO.NET
* Graceful degradation can be used to overcome temporary failures.

#### 4.3.1. Graceful degradation example:
* Reading data from a Redis cache and writing data to queue storage, where an Azure function synchronizes database with changes from the queue, acting like a CQRS/event sourcing system
* In this example, when the database is down users will see stale data, but their changes won't be lost, also it is eventually consistent.

### 4.4. Deployment automation:
* Deployment slots, infrastructure as code, Azure resource templates, chef

### 4.5. Scalability:
* Vertical scaling (scaling up) means upgrading a resource with another resource that has higher capacity (CPU, RAM, disk space, DTU, etc.), this means the old resource is detached and another one is installed, and it is not done automatically
* Horizontal scaling (scaling out) means adding more devices (nodes or instances) of the same capacity, done automatically and without interruption, so this is the preferred way for scaling
* We can use app service plan auto-scaling options to scale horizontally or vertically
* App service plan auto-scaling allows adding or removing instances based on selected metrics or for some time period (busy hours, busy season, etc.) and we can have more than one scaling conditions
* Function app (serverless Azure functions are always scalable)
* We can use an Azure load balancer, Azure application gateway, or Azure traffic manager for scalability
* Azure load balancer is layer 4 (TCP/UDP transport layer, works with IP addresses)
* Azure application gateway is layer 7 (application layer, can use cookie-based sessions, use URLs and HTTP headers for deciding where to route requests, supports web sockets, it also has a firewall to prevent SQL injection and cross-site scripting)
* Azure traffic manager (DNS level, can route in 4 routing methods: performance (chooses the least latency), weighted (round robin based on given percentages), priority (chooses the other endpoint if the first one is unhealthy), geographic (chooses the endpoint based on the user request region)
* Load leveling enables us to overcome CPU or disk overload spikes, by using a queue in between services to spread the resource-intensive processing over time.
* We can use queues for any resource/time-consuming operation, for asynchronous operations, and to decouple the system from its parts so that we can use different technologies for different parts of the system
* We can use partitioning on service bus and queues.
* We can use Azure API manager to control usage of APIs (any API hosted on a web app, API app, VM, or Azure function), like throttling requests for scalability and providing SLA to our 3rd party customers
* We can use Azure CDN to host static resources (files, images, videos, audio, HTML, CSS, javascript), they will be served from the location closest to the user
* We can use data partitioning on Azure storage
* We can use data partitioning on a database
* We can use Redis cache for improving read-performance and scalability
* We can use Azure functions for serverless scalability (Azure will match any load)

### 4.6. Using Azure CDN:
* When setting up Azure CDN, we assign an origin domain and path from where our CDN will fetch and cache files when users access them
* When using a CDN we should use our CDN endpoint address instead of relative addresses for resources in our URLs
* We should use version numbers in our filenames if the files will be changing over time
* We can also purge a file when we need to change that file, so that it is fetched again into the CDN

### 4.7. Data partitioning strategies:
* We need to evaluate partitioning strategies according to how we use our data and how it is structured, and also re-evaluate when our data grows
* Horizontal partitioning (sharding), where we can use a partition key to evenly map values or ranges on a key/column to different partitions, rows/documents with same data on the partition key go into the same partition, or we can assign ranges for a partition key, or we can use a round-robin approach to do this randomly, all partitions share same scheme (keys/columns)
* Vertical partitioning, where we can split a table for frequently used keys/columns and less frequently used ones, store these in different partitions, we can use small and faster storage or cache for the first one, and we can use large and slower cold storage for the second one
* Functional partitioning, where we can use different storage for different tables
* CosmosDB has horizontal partitioning by default, we need to choose a partition key for each table/document collection/graph, it does not have vertical partitioning (needs to be done manually/programmatically)
* Azure SQL database supports horizontal partitioning by choosing partition key, ranges, using an elastic pool and elastic map manager to automatically manage resources and to use a single query to use all shards
* Azure Redis cache supports horizontal partitioning (sharding)

### 4.8. Load performance testing:
* Performance test, under app service/development tools, allows us to do load performance tests (it requires a VSTS account)
* Here we choose the region where test requests will be generated from, user load (number of requests), and test duration, test type, and test URL
* Before running tests, it is recommended to add application insights to log more detailed information
* It is also recommended to do load tests in a testing or staging environment, instead of the production environment
* We can replicate an environment by grouping all its resources under a resource group
* Under application insights, we can see detailed information, like slow operations, details for each request, like request times and request dependencies (internal requests to other Azure resources like databases, queues, etc. made under this web request)

### 4.9. Testing in production (Canary release):
* Testing in production, under app service/development tools, allows us to do testing in production
* Here we can set a small percentage of the requests to go to the testing or staging environment (slot), and the rest to the production environment, so that we can test new features live side by side
* This style of testing is also called a canary release

## 5. CONTINUOUS DELIVERY (CI/CD) ON VSTS:
* VSTS (Visual Studio Team System) has nothing to do with Visual Studio or Team Foundation Server, these are all separate things
* Continuous delivery consists of continuous integration (build and unit testing) and continuous deployment (integration testing and release)
* VSTS offers agile tools, issue tracking, scrum/kanban boards, source control, private git repositories, continuous integration, continuous delivery, build and release definitions, testing and reporting for ios, Android, Linux, and windows programming tools/app stores
* Continuous delivery covers these steps: getting latest sources, building them, running unit tests, zipping artifacts, copying of artifacts to an output folder, copying artifacts to a deployment slot like testing or staging, running integration tests, and finally swapping this slot with the production slot
* VSTS .net build can be triggered manually, periodically, or by git commits from a local repo, VSTS or other git hosts
* VSTS .net build definition covers these tasks: nuget restore, build the solution (using MSBuild), select test assemblies (run unit tests), publish symbols path (publish .pdb files), and publish artifacts (artifacts will be ready for download, for manual deployment or automated release)
* Node.js with Gulp build definition covers these tasks: npm install, run gulp tasks (linting), archive files (zipping), publish artifacts
* VSTS Azure app service release can be triggered manually, periodically, or by VSTS build, or Jenkins build, it can also use Octopus deploy
* VSTS Azure app service release definition covers these tasks: get artifacts into the staging slot, run integration tests, swap staging slot with production

## 6. DOCKER CONTAINERS (INTRO):
* Docker is a popular software container
* Containers offer automated, fast, and reliable deployment of different components with their dependencies (software packages/framework/libraries/operating systems) on a machine
* Without using containers, we have to use a hypervisor to create virtual machines on a real machine, install the operating system and software on each VM in a duplicated way
* Containers especially help in deploying microservices (numerous small independent apps and services which make up a working software system)
* We build up a container image for each microservice and its dependencies, and then install these as containers into the container engine
* Containers also offer load balancing in container clusters, and containers can be duplicated easily for this purpose, and they can be signed for security and integrity

### 6.1. Docker terms:
* Docker hub is a Docker registry
* Docker hub contains Docker repositories (these are not similar to and share nothing with git repositories)
* Docker repository contains Docker images (images are tagged with version numbers or release names, etc.)


* Docker hub contains public official registries for popular software like node, dotnet, nginx, ubuntu, redis, etc.
* We can host our private registries using Docker cloud or Azure container registry

### 6.2. Docker CLI commands:
* `docker build` (builds an image from source files)
* `docker push` (uploads an image to docker hub)
* `docker pull` (downloads an image from docker hub)
* `docker images` (lists images)
* `docker run` (creates a new container and runs the image)
* `docker stop` (stops a running image without removing its container)
* `docker rm` (removes a container but keeps its image)
* `docker rmi` (removes an image)
* `docker ps` (lists currently running images)
* `docker ps -a` (lists currently running and stopped images)


We need to create a file named `Dockerfile` with information to dockerize our source (prepare our source for docker build)

If using Visual Studio, the `Dockerfile` for the project is created automatically, so we don't need to

## 7. CLOUD IDENTITY WITH AZURE AD:
* Web app authentication using OAuth and Open ID Connect on Azure AD:
* We create an Azure active directory on the Azure portal, here we give it a directory name and subdomain (which forms part of the domain URL)
* We switch to the Azure active directory we have created, from the top right directories menu or the top right dropdown menu under the Azure account icon and email
* Azure active directory is not associated with any Azure subscription, and does not have to be
* Our Azure AD is given a unique directory id (tenant id)
* We register our web app with our Azure AD, and our web app is given a client secret (client id)
* Our app is automatically registered with our Azure AD, if we select work or school account and Azure AD in Visual Studio / New project / ASP.NET Core Web Application
* If we deploy to another environment or to another Azure AD, we need to edit the domain URL, tenant id, and client id inside the appsettings.json file in our solution and also change/add callback path on Azure portal

### 7.1. Web app authentication flow:
* When an anonymous user (user with no authentication cookie) visits our web app, the user will be redirected to https://login.microsoft.com/{tenant_id}/oauth2/v2.0/authorize?client_id={client_id} (with the unique tenant id of our Azure AD and client id of our web app in the URL)
* After the login and permissions consent (for our web app to see the user's name, email, etc.) page of the OAuth provider (Microsoft), the user is redirected to our web app with a digitally signed JWT token, which contains the user ID and auth claims (like name, email, etc.)
* If our ASP.NET Core web app middleware can validate the JWT token, the user is considered authenticated and our cookie middleware will generate an authentication cookie, which will be sent back and forth on the HTTP header, between the user request and our web app response from then on

### 7.2. Web API authentication using OAuth and Open ID Connect on Azure AD:
* Azure AD definition is the same as we did for the web app example
* Our app is automatically registered with our Azure AD, if we select work or school account and Azure AD in Visual Studio / New project / ASP.NET Core Web Application / API

### 7.3. Web API authentication flow when called from a web app:
* Our web app acquires a digitally signed JWT bearer token from our Azure AD using a client secret, and keeps it for using further in its requests to our web API
* When an unauthorized request (request with no JWT bearer token in its header) comes to our web API, the request will be rejected
* If our ASP.NET Core web API middleware can validate the JWT bearer token, the request is considered authenticated
