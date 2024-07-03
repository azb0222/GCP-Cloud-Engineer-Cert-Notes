## BILLING 

**Payments Profile**: central account to store all billing, payment information 

**Billing Account**: 
- Cloud level resource
- Linked with Payments Profile
- Tracks all costs incurred by GCP usage 
- Can create multiple Billing Accounts (ie. hierarchial within Organization)
- Two types: 
  - 1) Self-servce accounts: paid by credit/debit card or bank account
  - 2) Invoiced: invoices sent to customers (reccomended for enterprises)
- IAM Roles:
  - Types: 
    - Organization Level: 
      - `roles/billing.creator`: Create new self-service Billing Accounts (reccomended for those w/ financial role). 
    - Organization/Billing Account Level: 
      - `roles/billing.admin`: CRUD payment info, pricing info, etc. for Billing Accounts. Cannot create Billing Accounts (reccomended for those w/ general cloud admins). 
      - `roles/billing.costsManager`: CRUD budgets, Billing Account info. Cannot view pricing info. 
      - `roles/billing.user`: Can link projects to Billing Accounts (reccomended for anyone w/ `roles/project.creator`).
      - `roles/billing.viewer`: Can view billing account cost + transactions. 
  - Modify Billing Account Roles in Console: Billing -> choose Billing Account -> Billing Account Overview -> Manage Billing Account \ https://www.youtube.com/watch?v=Vti0OGQfLHQ

### Billing Budgets and Alerts 
Can set billing alerts for a specific billing account 
- Three alert %s by default: 50%, 90%, 100%

### Exporting Billing Data 
Billing data for a billing account can be exported to BigQuery database or CloudStorage file 
#TODO: learn how to analyze in BigQuery 

#TODO: learn how to use Cost Calculator


