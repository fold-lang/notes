# Parsing CSV

CSV parsing is an apparently simple task that hides many small details that can result in a complex implementation.

We can build a naive version that will split the rows by the new-line character and the fields with commas. This is the most efficient approach but also the most fragile because it accepts a very limited input format. Silently breaking if the input is malformed.

On the other hand our parser can support all possible format variations and validations. Resulting in additional cost.
