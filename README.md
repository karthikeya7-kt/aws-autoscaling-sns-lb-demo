# AWS AutoScaling sns lb Demo

**Description:**  
This project uses EC2 with a Load Balancer and Auto Scaling Group monitored through CloudWatch. When CPU utilization exceeds a threshold, CloudWatch triggers an SNS topic to send an email alert. The setup demonstrates automated monitoring, scaling, and alerting using AWS Free Tier services.

## Technologies Used
Amazon EC2 – for hosting the base instance and creating the AMI
Amazon AMI – used to launch identical instances through the Launch Template
Launch Template – defines instance configuration for the ASG
Application Load Balancer (ALB) – distributes traffic across instances
Target Group – health checks and routing for ALB
Auto Scaling Group (ASG) – automatically scales instances based on CPU load
Amazon CloudWatch – monitors CPU metrics and triggers alarms
SNS (Simple Notification Service) – sends email alerts based on CloudWatch alarms
VPC + Subnets (default) – for networking components used by ALB and ASG
Security Groups – to allow HTTP access and control instance traffic
AWS Free Tier Resources – used throughout to keep cost at zero

## Architecture
 
                   +------------------------+
                   |    Application User    |
                   +-----------+------------+
                               |
                               v
                   +------------------------+
                   | Application Load       |
                   |      Balancer (ALB)    |
                   +-----------+------------+
                               |
                 +-------------+-------------+
                 |                           |
                 v                           v
        +----------------+           +----------------+
        | EC2 Instance   |           | EC2 Instance   |
        | (from AMI via  |           | (Auto-Scaled)  |
        | Launch Template)|          |                |
        +--------+-------+           +--------+-------+
                 |                           |
                 +-------------+-------------+
                               |
                               v
                   +------------------------+
                   |  Auto Scaling Group    |
                   +-----------+------------+
                               |
                               v
                   +------------------------+
                   |     CloudWatch Alarm   |
                   +-----------+------------+
                               |
                               v
                   +------------------------+
                   |        SNS Topic       |
                   |   (Email Notification) |
                   +------------------------+


## What I built
- EC2 instance configured as a webserver
- Created AMI, Launch Template
- Application Load Balancer (ALB) + Target Group
- Auto Scaling Group (ASG) with scale-out/scale-in policies
- CloudWatch alarms to monitor CPU
- SNS topic to send email alerts on threshold breach

## How I tested
1. Launched webserver and created AMI.  
2. Created Launch Template and Auto Scaling Group attached to ALB.  
3. Triggered CPU load using `stress` to force autoscaling.  
4. Verified CloudWatch spike, ASG activity, and SNS email.

## Files / Screenshots
See `/screenshots` for console evidence: EC2, AMI, ALB DNS, Target Group, ASG, CloudWatch graph, and stress terminal.

## Notes

1. Launch EC2 Instance

Launched a t2.micro instance (Free Tier).
Installed webserver / configured environment.
Verified it runs properly.

2. Create AMI

Created an AMI from the EC2 instance to preserve configuration.
AMI is used as the base image for scaling.

3. Create Launch Template

Used the AMI as the template image.
Configured instance type, security group, user data if needed.

4. Create Target Group

Target type: Instances.
Health check: HTTP on port 80.
Will attach EC2 instances created by ASG.

5. Create Application Load Balancer (ALB)

Chose Application Load Balancer.
Attached the Target Group.
ALB distributes traffic across instances.

6. Create Auto Scaling Group (ASG)

Connected ASG to Launch Template + Target Group.
Set capacity: min = 4, desired = 4, max = 10.
ASG automatically launches new instances when CPU is high.

7. Create CloudWatch Alarm

Metric: CPUUtilization of ASG instances.
Threshold example: > 70% for 2 minutes.
Action: Trigger SNS notification.

8. Create SNS Topic

Created an SNS Email Topic.
Subscribed using email → confirmed subscription.
CloudWatch alarm triggers this SNS topic.

9. Trigger Scaling

Used stress or CPU load to increase usage.
Observed CloudWatch spike, ASG launching new instance.
Received SNS email alert.

10. Test & Cleanup

Tested ALB DNS, observed traffic distribution.
Deleted AMI, snapshots, ASG, ALB after testing.

