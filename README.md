# Enable-Cross-Region-Backup-Replication-for-EC2using-AWS-Backup

# Introduction
This project demonstrates how to configure cross-region backup replication for an Amazon EC2 instance using AWS Backup.
The objective is to ensure data durability, disaster recovery readiness, and business continuity by automatically copying EC2 backups from a primary AWS region to a secondary (disaster recovery) region.

This project is designed and written in a beginner-friendly way, explaining each step clearly without assuming prior AWS Backup experience.

# Architecture Overview

-  Source Region: Asia Pacific (Mumbai) - ap-south-1
-  Destination (DR) Region: US East (N. Virginia)- us-east-1
-  Primary Backup Vault:                                 

                          primary-ec2-vault
- Replica Backup Vault: 

                     replica-ec2-vault
- IAM Role:

                  AWSBackupDefaultServiceRole
- Resource Assignment:  Tag-Based (Backup=True)

# Steps to Deploy (Beginner-Friendly & Detailed) 
## 🔹 Step 1: Launch and Prepare the EC2 Instance
1. Log in to the AWS Management Console.
2. Navigate to EC2 → Instances → Launch instance.
3. Choose Amazon Linux AMI.
4.Select instance type         
                         
                         t2.micro.

5. Configure security group:
   - Allow SSH (22)
   - Allow HTTP (80)
6. Launch the instance.
  Install Apache and create test data:

            sudo yum update -y
            sudo yum install httpd -y
            sudo systemctl start httpd
            sudo systemctl enable httpd
           echo "Backup validation file" | sudo tee/var/www/html/backup_test.txt

## 🔹Step 2: Create Backup Vault (Source Region)
1. Go to AWS Backup → Backup Vaults.
2. Ensure region is ap-south-1.
3. Click Create backup vault.
4. Name:

                      primary-ec2-vault.
5. Use default encryption.
## Step 3: Create Backup Vault (Destination Region)
1. Switch region to us-east-1.
2. Go to AWS Backup → Backup Vaults.
3. Click Create backup vault.
4. Name: 

                   replica-ec2-vault.

## 🔹 Step 4: Create Backup Plan
1. Go to AWS Backup → Backup Plans.
2. Click Create backup plan.
3. Choose Build a new plan.
4. Name it

                ec2-cross-region1-plan.
## 🔹Step 5: Configure Backup Rule
1. Click Add backup rule.
2. Rule name: 

          ec2-backup-rule.
3. Backup vault: 

           primary-ec2-vault.
4. Choose Daily or On-demand schedule.
5. Save rule.
## 🔹 Step 6: Add Cross-Region Copy Rule
1. Scroll to Copy to destination.
2. Click Add copy rule.
3. Destination region: 

                us-east-1.
4. Destination vault: 

                 replica-ec2-vault.
5. Set retention (7–35 days).
6. Save and create plan.
## 🔹 Step 7: Tag the EC2 Instance (Important)
1. Go to EC2 → Instances → Select instance.
2. Open Tags → Manage tags.
3. Add:
       
       -Key: Backup
       -Value: True
## 🔹 Step 8: Assign EC2 to Backup Plan
1. Go to AWS Backup → Backup Plans → ec2-cross-region-plan.
2. Click Assign resources.
3. Assignment method: Tag-based.
4. Tag:

            Backup=True.
5. IAM Role:

             AWSBackupDefaultServiceRole.
6. Save assignment.
## 🔹 Step 9: Trigger On-Demand Backup
1. Go to AWS Backup → Protected resources.
2. Select EC2 instance.
3. Click Create on-demand backup.
4. Select 

               primary-ec2-vault.
5. Click Create backup.
## 🔹 Step 10: Monitor Backup Jobs
1. Go to AWS Backup → Jobs.
2. Verify:
        -Backup job → Completed
        -Copy job → Completed
## 🔹 Step 11: Verify Recovery Point in DR Region
1. Switch to us-east-1.
2. Open replica-ec2-vault → Recovery points.
3. Confirm EC2 recovery point exists.
## 🔹 Step 12: Restore & Validate (Optional)
1. Restore EC2 from DR region recovery point.
2. SSH into restored instance.
3. Validate backup:

              cat /var/www/html/backup_test.txt
  ## ⚠️ Issues Encountered and Resolutions
- On-demand backup option missing in plan → Used Protected Resources.
- Copy job not triggered → Switched to tag-based assignment.
- Backup rule not applying → Recreated backup plan properly.
- IAM role missing → Created -AWSBackupDefaultServiceRole manually.
- AWS Backup UI inconsistencies → Avoided templates.            

## 📝 Summary
This project successfully implements cross-region EC2 backup replication using AWS Backup.
It ensures disaster recovery, regional fault tolerance, and data protection by maintaining replicated backups in a secondary region.