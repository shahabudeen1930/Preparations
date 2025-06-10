#### Devops Role & Responsibilities

- Thanks for opportunity,
     - **Hi, i am ****** I have total 14+ Years of IT experience in which I have 6+ yrs of experience in SRE & on DevOps tools.** Network Operations Center
     - I have experience working with multiple cloud providers, AWS, Azure, and Oracle Cloud (OCI).
- I was responsible for:
     - I support various cloud services, Virtual Machines (VMs), EC2, VPC, VNet, IAM policy management, RBAC,  Route 53, DNS, EKS, AKS, Docker, Storage,.
     - **CI/CD Pipeline Implementation:** Designed and implemented a Jenkins, Azure pipelines, GitHub Actions/GitLab CI/CD pipeline for automated deployments.
     - **Deployment & Management:** Deployed and managed containerized applications using Kubernetes (EKS/AKS), Helm charts with proper RBAC and security policies.
     - Infrastructure Automation: Used Terraform & Ansible to provision and configure cloud resources and also used Shell/Bash Script for automating 
     - Monitoring & Alerting: monitoring infrastructure and applications continuously, Prometheus, Grafana, Azure Monitoring, CloudWatch, ELK Stack for real-time monitoring and proactive alerting.
     - Incident Management & RCA: Led blameless postmortems and SLO/SLI/SLA tracking to improve system reliability.
     - Security & Compliance: Implemented IAM, RBAC, network policies, vulnerability scanning, and secrets management using HashiCorp Vault.
     - In a Role of SRE : Cost Optimization: Leveraged autoscaling, Spot Instances, rightsizing, serverless patterns to optimize cloud usage and cost.
     - and as a system administration i have expreience in server provisioning, migrations, troubleshooting, Harware related and performance tuning.

#### Deployment of Applications
- yeah,  I’ve been actively involved in deploying various types of applications across different environments. I’ve handled deployments using Kubernetes (EKS/AKS) where I used Helm charts to package and deploy microservices. I’ve also worked with CI/CD tools like Jenkins, GitHub Actions, and Azure Pipelines to automate the entire deployment process — from code commit to production release.
- In most cases, I’ve integrated proper testing, approvals, rollback strategies to ensure zero-downtime deployments. I’ve deployed containerized applications with proper RBAC, network policies, and monitoring integrations in place. Depending on the use case, I’ve also done blue-green and canary deployments to minimize risk.
- I ensure that deployments are automated, repeatable, and aligned with infrastructure and security best practices.

#### Deployment Strategies
- Topics
     - Blue-Green Deployments (Minimizes downtime)
     - Canary Deployments (Progressive rollout)
     - Rolling Updates (Zero-downtime upgrades)
     - Immutable Infrastructure (Terraform, Ansible)

#### Deployment steps
- I have deployed multiple web applications, including microservices-based applications on Kubernetes (EKS, AKS) and monolithic applications on VMs and containers. I automated deployments using Jenkins, GitHub Actions, and Terraform, implementing blue-green and canary deployments to ensure zero downtime.
- For monitoring and alerting, I set up Prometheus, Grafana, and ELK, integrating with Datadog for proactive monitoring. I also enforced Kubernetes security best practices, including RBAC, secrets management (Vault), and network policies.
- One of the key challenges I faced was a high-traffic application experiencing downtime during peak hours. I optimized autoscaling in EKS with HPA and VPA, reducing latency by 40% while ensuring cost efficiency.
- Additionally, I have worked on incident response and RCA processes, ensuring production stability by implementing self-healing mechanisms and automated rollback strategies.
- **Types of Applications Deployed**
     - Microservices-based Applications (Docker + Kubernetes)
     - Monolithic Applications (Traditional VM-based or containerized)
     - Serverless Applications (Lambda, Azure Functions)
     - Stateful Applications (Databases, message queues)
     - High-Traffic Web Applications (E-commerce, SaaS, FinTech)
- **Technologies & Tools Used**
     - CI/CD Pipelines: Jenkins, GitHub Actions, Azure DevOps, GitLab CI
     - Containerization & Orchestration: Docker, Kubernetes (EKS/AKS)
     - Infrastructure as Code (IaC): Terraform, Ansible
     - Monitoring & Logging: Prometheus, Grafana, ELK, Datadog, Splunk
     - Security & Compliance: IAM, RBAC, Vault, OPA, Falco
- **Cloud Platforms & Integrations**
     - AWS: Deployed on EC2, EKS, ECS, Lambda
     - Azure: AKS, App Service, Azure Functions
     - GCP: GKE, Cloud Run
- **Incident Management & Troubleshooting**
    - Handled Production Outages
    - Implemented Self-Healing Mechanisms
    - Optimized Cost & Performance
    - Root Cause Analysis (RCA) and Postmortem
 
#### Cost optimization in DevOps
- points
     - **Auto-scaling**: Reduce idle resources.
     - **Spot Instances**: Use AWS EC2 Spot for non-critical workloads.
     - **Resource Quotas**: Prevent over-provisioning in Kubernetes.
     - **Monitor Usage**: AWS Cost Explorer, Azure Cost Management.

#### Optimizations in realtime - CPU & high pod restarts
- In one of my recent projects, we were managing a microservices-based application hosted on Azure Kubernetes Service (AKS). We noticed frequent CPU throttling and high pod restarts, especially during peak traffic hours, which affected reliability.
- After investigating, we found that resource requests and limits for several services were either too low or misconfigured. I implemented a resource optimization initiative: used Prometheus and Grafana to monitor actual CPU/memory usage over time, then fine-tuned the resource limits based on that data. Additionally, I introduced horizontal pod autoscaling to scale the services based on traffic patterns.
- As a result, we reduced pod restarts by over 80%, improved system stability, and decreased infrastructure cost by around 25% by eliminating over-provisioning.

#### Optimizations in realtime - application performance
- In one of my recent projects, we were managing infrastructure on Azure using Terraform for IaC. Our application was deployed across multiple services including Azure Kubernetes Service (AKS), Azure App Services, and Azure SQL.
- We noticed that during peak usage, the application performance degraded and response times were high. After digging into Azure Monitor and Application Insights, we identified that our AKS cluster was over-provisioned in some areas and under-provisioned in others, leading to resource wastage and instability.
- To optimize this, I implemented the following:"
     - Used Terraform to refactor our infrastructure code, making it more modular and parameterized to easily scale services based on load.
     - Enabled Horizontal Pod Autoscaling in AKS and defined appropriate CPU/memory thresholds based on historical metrics from Prometheus and Grafana.
     - Configured Azure Load Testing to simulate peak traffic and validate the improvements.
     - Reduced over-provisioning by analyzing metrics and rightsizing the VMs using Azure Advisor and Terraform updates.
     - Automated AKS node pool scaling using KEDA for event-driven workloads.
- As a result, we reduced monthly infrastructure costs by around 20%, improved system stability, and cut down latency by nearly 40% during high-traffic periods.

#### End-to-End deployment Workflow
- Process
     - Provision Infra → Terraform creates AKS, VMs, Storage, Network.
     - Code Management → Devs push code to GitHub/GitLab.
     - CI/CD Pipeline → Jenkins/Azure DevOps builds, scans, and deploys.
     - Security & Compliance → Use Trivy, SonarQube, Aqua.
     - Deployment & GitOps → Deploy via Helm & ArgoCD.
     - Monitoring & Logging → Prometheus, Grafana, ELK track performance.
     - Scaling & HA → HPA, Azure Load Balancer ensure uptime.
     - Incident Management → Alertmanager, PagerDuty, Slack notifications
