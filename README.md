 # Azure RBAC Misconfiguration Simulation 🔐

How excessive permissions led to a production resource deletion

Why I built this project 💡

I wanted to move beyond theory and actually put AZ-104 concepts into practice. A lot of real cloud incidents are not caused by hackers or malware. They happen because someone was given more access than they should have.

This project simulates a realistic RBAC mistake where an employee is over-permissioned and uses that access to delete a production resource. No exploit. No malware. Just access doing exactly what it was allowed to do.

<p align="center">
  <img src="diagram.png" width="750">
</p>


Environment 🧩

Microsoft Entra ID

Azure RBAC

Azure Storage

Azure Activity Log

Azure Monitor (alerts)

Step 1: I created the identities 👤

I started by creating two normal users to reflect a real environment. One represents a legitimate admin and the other represents an employee who will later receive too much access.

I created:

manager.user@yourtenant.onmicrosoft.com

employee.user@yourtenant.onmicrosoft.com

At this stage, I deliberately assigned no roles to keep a clean baseline.

📸 Screenshot 1 – Entra ID users created (ManagerUser and EmployeeUser)
Insert screenshot here

Step 2: I created a production resource 🏗️

To make the scenario meaningful, I created a simple production resource that should not be deleted.

I created a resource group called rg-production and a storage account named prodstorage001 inside it. I kept default settings on purpose so the focus stayed on access control, not configuration complexity.

📸 Screenshot 2 – Production resource group and storage account created
Insert screenshot here

Step 3: I introduced an RBAC mistake (on purpose) ⚠️

This is where many real incidents begin. An admin needs to quickly grant access and assigns a powerful role at the wrong scope.

I assigned the Contributor role to employee.user at the subscription level. This gave the employee the ability to delete most resources across the environment.

This wasn’t malicious. It was a scoping mistake.

📸 Screenshot 3 – Employee assigned Contributor role at subscription scope (misconfiguration)
Insert screenshot here

Step 4: I simulated the insider incident 🚨

To prove this was real, I switched identities.

I signed in as employee.user using a private browser session and navigated to the production resource group. From there, I deleted the storage account or the entire resource group.

The deletion succeeded. Nothing was bypassed. The permissions allowed it.

📸 Screenshot 4 – Employee successfully deletes production resource
Insert screenshot here

Step 5: I investigated what happened 🔍

Next, I acted as a security engineer responding to the incident.

I switched back to the admin account, opened the Azure Activity Log, and filtered for delete operations. The logs clearly showed the deletion, the affected resource, the time, and the identity that performed the action.

Azure recorded everything.

📸 Screenshot 5 – Activity Log showing deletion performed by EmployeeUser
Insert screenshot here

Step 6: I fixed the root cause 🛠️

Detection alone isn’t enough. The real fix is correcting access.

I removed the Contributor role from employee.user at the subscription level and replaced it with a least-privilege role scoped only to the required storage resource.

This ensured the employee could still do their job without being able to delete production resources.

📸 Screenshot 6 – Contributor role removed and least-privilege access applied
Insert screenshot here

Step 7: I added monitoring with an alert rule 📢

After fixing permissions, I wanted to go one step further and improve how this environment would respond in the future.

As a security engineer, preventing incidents is important, but detecting them early and automatically is just as critical.

I created an Azure Monitor alert rule that triggers when a delete operation occurs on key resources. This ensures that if a similar action happens again, it doesn’t go unnoticed and can be investigated immediately.

This step shows maturity in security thinking. It shifts the environment from being reactive to being monitored and observable.

📸 Screenshot 7 – Alert rule for resource deletion events
Insert screenshot here

Key takeaways ✅

RBAC misconfigurations are a major cloud security risk

Many incidents happen without hacking or malware

Azure provides strong visibility through activity logs

Least privilege reduces blast radius

Monitoring and alerting close the detection gap
