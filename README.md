# Azure Lighthouse Onboarding (Public Deploy Assets)

Public deployment assets for delegating a customer Azure subscription to **Entre Technology Services** via Azure Lighthouse.

These files are published publicly because the Azure portal fetches them unauthenticated when the **Deploy to Azure** button is used. They contain no secrets. Documentation and change control are maintained internally.

## Deploy to Azure

[![Deploy To Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FEntre-Technology-Services%2Fazure-lighthouse-onboarding-public%2Fmain%2FmainTemplate.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FEntre-Technology-Services%2Fazure-lighthouse-onboarding-public%2Fmain%2FcreateUiDefinition.json)

## What this grants

The deployment creates a Lighthouse delegation at **subscription scope** to the Entre managing tenant `55705222-2816-4f25-8c0e-f9fa312aa10c`. No resources are created or modified in the delegated subscription.

| Group                         | Role                                                 |
| ----------------------------- | ---------------------------------------------------- |
| SG-AzureLH-ReadOnly           | Reader                                               |
| SG-AzureLH-Operations-Admins  | Contributor                                          |
| SG-AzureLH-Operations-Admins  | Managed Services Registration Assignment Delete Role |
| SG-AzureLH-Commitments-Admins | Cost Management Contributor                          |
| SG-AzureLH-Commitments-Admins | Reservation Purchaser (purchase right only)          |

**User Access Administrator is intentionally not delegated.** Entre cannot grant itself or anyone else additional RBAC on the subscription through this delegation.

## Technician workflow

1. Click **Deploy to Azure** above.
2. When prompted, sign in to the **customer's** Azure tenant with an account that holds **Owner** on the target subscription — the customer's owner account, or the customer-tenant break-glass account per Entre's internal SOP. An Entre guest account will not work; it lacks the rights to create the delegation.
3. Select the **subscription** to delegate, and a **deployment region** if prompted. Lighthouse resources are not regional; any region is valid.
4. Enter the **Customer name**. This is the delegation (offer) name, visible to Entre under *Azure Lighthouse > My customers* and to the customer under *Service providers*. Must be unique per subscription (e.g., `Bank of the Rockies`). The **Delegation description** field is optional and can be left at its default.
5. Review and select **Create**.
6. Verify in the Entre tenant: **Azure Lighthouse > My customers** shows the customer. If it does not appear, check **Portal Settings > Directories + Subscriptions** and include the customer directory / All subscriptions.
7. Sign out of the customer tenant. All day-2 work is done from the Entre tenant via Azure Lighthouse.

The `Microsoft.ManagedServices` resource provider is registered automatically by the deployment form. No manual registration step is required.

One deployment per subscription. Re-running for the same customer name is idempotent — registration names are deterministic GUIDs derived from the offer name.

## Removing the delegation

The customer can revoke access at any time without Entre's involvement: **Service providers > Service provider offers**, select the offer, and delete it. Access stops immediately.

Entre can also remove its own delegation, via the Managed Services Registration Assignment Delete Role held by `SG-AzureLH-Operations-Admins`.

## Files

| File                      | Purpose                                                          |
| ------------------------- | ---------------------------------------------------------------- |
| `mainTemplate.json`       | Subscription-scope Lighthouse delegation (customer-agnostic).     |
| `createUiDefinition.json` | Portal form: subscription picker, customer name, description.     |
