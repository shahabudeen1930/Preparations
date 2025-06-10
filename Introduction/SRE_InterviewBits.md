#### SRE Role & Responsibilities
- Key points
     - **Ensure System Reliability & Uptime** : Maintain high availability and performance of critical systems through proactive monitoring, alerting, and incident response, aligned with defined SLOs and SLAs.
     - **Infrastructure as Code (IaC) & Automation** : Design and manage cloud infrastructure using tools like Terraform and Ansible, along with scripting (Bash, Python) to automate repetitive tasks and reduce human error.
     - **CI/CD Pipeline Implementation & Optimization** : Build and maintain robust CI/CD pipelines using Jenkins, GitHub Actions, GitLab CI, or Azure Pipelines to enable rapid, reliable, and automated software delivery.
     - **Containerization & Kubernetes Management** : Deploy and manage containerized applications using Docker, Kubernetes (EKS/AKS), and Helm, applying best practices for RBAC, network policies, and security.
     - **Monitoring, Logging & Observability** : Set up and manage monitoring and alerting systems using Prometheus, Grafana, CloudWatch, Azure Monitor, and the ELK Stack to ensure full visibility into system health and performance.
     - **Incident Management & Root Cause Analysis** : Lead on-call rotations, quickly respond to incidents, perform root cause analysis (RCA), and conduct blameless postmortems to continuously improve service reliability.
     - **Security & Compliance** : Implement security controls like IAM, RBAC, vulnerability scanning (e.g., Trivy), and manage secrets securely using HashiCorp Vault, ensuring compliance with industry standards.
     - **Cost Management & Optimization** : Analyze cloud usage and optimize resources using autoscaling, Spot Instances, serverless patterns, and resource rightsizing to minimize operational costs.
     - **Cross-Functional Collaboration & DevOps Culture** : Work closely with development, QA, and product teams to promote a DevOps mindset, enabling faster releases, shared ownership, and continuous improvement across the delivery lifecycle.

#### SRE - Site Reliability Engineer
- SRE is to improve system reliability and availability we would work on automation, monitoring, performance optimization.

1. Reliability and Availability:
     - Ensure systems are highly available and reliable.
     - Define and monitor SLOs (Service Level Objectives), SLIs (Service Level Indicators), and SLAs (Service Level Agreements).
2. Incident Management and Response:
     - Quickly respond to outages and incidents.
     - Perform root cause analysis and postmortems to prevent recurrence.
3. Automation and Tooling:
     - Automate manual tasks (toil) using scripts or tools.
     - Build CI/CD pipelines, auto-scaling systems, and self-healing mechanisms.
4. Monitoring and Observability:
     - Implement robust monitoring using tools like Prometheus, Grafana, or Datadog.
     - Ensure end-to-end visibility across systems with proper logging and alerting.
5. Performance and Capacity Planning:
     - Identify performance bottlenecks and optimize systems.
     - Plan for future capacity needs using data-driven insights.
6. Collaboration and Culture:
     - Work closely with dev teams to make systems more reliable.
     - Promote blameless postmortems and a culture of learning and continuous improvement.
7. Change Management:
     - Review and implement changes in a safe and controlled manner.
     - Use canary deployments, feature flags, and rollback mechanisms.

#### SRE Daily tasks - day-to-day
     - **Morning:** Review alerts, attend standups, check system health.
     - **Midday:** Work on automation, CI/CD, and infrastructure improvements.
     - **Afternoon:** Handle incidents, perform debugging, optimize costs, and review security.
     - **End of Day:** Update documentation, plan for reliability improvements, and assess monitoring dashboards.

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
