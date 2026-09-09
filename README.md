# Azure Lighthouse Onboarding (Public Deploy Assets)

Public deployment assets for delegating a customer Azure subscription to
**Entre Technology Services** via Azure Lighthouse. These files are published
publicly because the Azure portal fetches them unauthenticated when the
**Deploy to Azure** button is used. They contain no secrets.

Source of record and documentation: the private `azure-lighthouse-onboarding`
repository.

## Deploy to Azure

[![Deploy To Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FEntre-Technology-Services%2Fazure-lighthouse-public%2Fmain%2FmainTemplate.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FEntre-Technology-Services%2Fazure-lighthouse-public%2Fmain%2FcreateUiDefinition.json)

## Technician workflow

1. Click **Deploy to Azure** above.
2. When prompted, sign in and **switch to the customer's directory**.
3. Select the **subscription** to delegate.
4. Enter the **Customer name** (e.g., Bank of the Rockies).
5. Review and select **Create**.
6. Verify in the Entre tenant: **Azure Lighthouse -> My customers** shows the
   customer. If it does not appear, check **Portal Settings -> Directories +
   Subscriptions** and include the customer directory / All subscriptions.

The `Microsoft.ManagedServices` resource provider is registered automatically by
the deployment form, so no manual registration step is required.

## Roles delegated

| Group | Role |
| --- | --- |
| SG-AzureLH-ReadOnly | Reader |
| SG-AzureLH-Operations-Admins | Contributor |
| SG-AzureLH-Operations-Admins | Managed Services Registration Assignment Delete Role |
| SG-AzureLH-Commitments-Admins | Cost Management Contributor |
| SG-AzureLH-Commitments-Admins | Reservation Purchaser (purchase right only) |

Managing tenant: Entre Technology Services (55705222-2816-4f25-8c0e-f9fa312aa10c).

## Limitations (see standard and lessons-learned in the private repo)

- User Access Administrator is intentionally NOT delegated. Through Lighthouse it
  does not provide general-purpose RBAC administration.
- Reservation Purchaser grants purchase rights only. Existing reservation orders
  are tenant-scope (/providers/Microsoft.Capacity) and do not appear through
  Lighthouse.
- One deployment per subscription. Re-running for the same customer name is
  idempotent (deterministic GUID names).

## Files

- `mainTemplate.json` - subscription-scope Lighthouse delegation (customer-agnostic).
- `createUiDefinition.json` - portal form (customer name + subscription picker).

## Rebuilding the button URL

Portal URL shape (both file URLs URL-encoded):

    https://portal.azure.com/#create/Microsoft.Template/uri/<ENCODED_mainTemplate>/createUIDefinitionUri/<ENCODED_createUiDefinition>

Raw file URLs (branch: main):

    https://raw.githubusercontent.com/Entre-Technology-Services/azure-lighthouse-public/main/mainTemplate.json
    https://raw.githubusercontent.com/Entre-Technology-Services/azure-lighthouse-public/main/createUiDefinition.json
