flowchart TD
  Start[Start] --> LoginForm[Login Form]
  LoginForm --> AuthCheck{Authenticated?}
  AuthCheck -->|No| LoginForm
  AuthCheck -->|Yes| RoleCheck{Role?}
  RoleCheck -->|Student| StudentDashboard[Student Dashboard]
  RoleCheck -->|Treasurer| TreasurerDashboard[Treasurer Dashboard]
  StudentDashboard --> SubmitPayment[Submit Payment Page]
  SubmitPayment --> PaymentForm[Payment Submission Form]
  PaymentForm --> SubmitRequest[Submit Payment Request]
  SubmitRequest --> PendingState[Payment Pending Approval]
  StudentDashboard --> ExpensesPage[Expenses Page]
  TreasurerDashboard --> ViewPayments[View Pending Payments]
  ViewPayments -->|Approve| ApproveAction[Approve Payment]
  ApproveAction --> UpdateApproved[Update Status Approved]
  ViewPayments -->|Reject| RejectAction[Reject Payment]
  RejectAction --> UpdateRejected[Update Status Rejected]
  UpdateApproved --> ViewPayments
  UpdateRejected --> ViewPayments
  StudentDashboard --> ExpensesPage
  TreasurerDashboard --> ExpensesPage
  ExpensesPage --> ExpensesList[Expenses Data Table]