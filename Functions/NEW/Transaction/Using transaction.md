# What does it do? 
`transaction` can be used to group a series of events into the same unit. 
An example could be to use transaction to group all event codes belonging to a specific user into the same event. 

I have not yet found a great use case for this. 

**Example:**
- At 5:00, the user Bob triggered event codes 4624 and 4634. `transaction` would then group these events into the same event. 