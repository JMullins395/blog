---
layout: post
title: "Lab 6 Reflection"
author: Jack Mullins
---

Lab 6 was an interesting lesson for me in the price of overthinking.  In the initial stages of this, long before making this blog post, I went through several iterations of my original ER diagram, with the first attempt being much too complicated and granular.  Ultimately, I had to stop sweating some of the semantics so much in order to get something meaningful done.  The following paragraphs are my additional reflections on this experience.

### Entities

For this grocery store scenario, I decided on a 4-entity system:
1. "Items": the items in the grocery store's inventory
2. "Order": the mechanism by which the customer selects the items that they want and arranges a given date and time of fulfillment.
3. "Customers": the consumers, who add items to orders and then place orders.
4. "Select Item" ("SelectedItems" in the Schema): an "action" or "decision" entity, through which items and their details are retrieved, the user adds details, and the sum of the details is associated with an order the user places.

### Assumptions

In the course of making my original Lucid Chart ER diagram, as I said before, I struggled to adhere to the maxim of "keep it simple, stupid" and indulged in a rather detrimental tendency to focus far too much attention on what were ultimately irrelevant details.  Several rounds of revision, after going to office hours, led to the image being the end iteration of my ER diagram depiction of the "grocery store" description given on the assignment page:
![Could not load image](/assets/images/lab_6_diagram.png)

In the course of taking this and using it to create an SQL Schema in Redgate, there were some assumptions and alterations that I had to make for things to be translated properly.  First, I had to add something that turned out to be missing from my ER diagram: ID attributes for each of my entities.  Once I made this first assumption, I was able to cease my stewing over "how do I connect these when there are no attributes already in common?"  My subsequent assumptions had to do with relationships between my entities, with regards to which keys would appear in multiple places and necessitate a graphical connection.  I was able to *mostly* follow the three paths from my ER diagram--these becoming the one or many to one connections from "SelectedItems" (revised name of the "select item" action from the ER diagram) to "Items" and "Order", and the one to many-or-one connection from "SelectedItems" to "Customers".  However, critically, I had to make one new connection in order to properly map PKs and FKs, which was a new one to one-or-many relation of customers to order (one customer makes one or more orders).  Additionally, the "order items" attribute of orders was removed from "Order," with the assumption that "SelectedItems" handles the job of tracking the line items of the order and their quantities.  After these major assumptions, this is the SQL Schema I ended up with:
![Could not load image](/assets/images/lab_6_schema.png)

Overall, I was quite satisfied with my data representation at this ending stage.

### User Stories

The following are some user stories for the scenario description given on the assignment page:

1. Customers can select one or more items to add to one order.
    * Size: 5 (important)
    * Connections: the specific instructions in user story 5 are also set by the user when they add an item in the same "select items command"; same mechanism retrieves items and facilitates their being added to the customer's order

2. Customers can make more than one order.
    * Size: 3 (relatively important function)
    * Connections: one customer is not restricted to one order, allowing for multiple purchases; item inventory has to be updated each time more items are withdrawn by a new order.

3. Orders cannot be changed once they are finalized.
    * Size: 5 (important element of the store's system - required for order fulfillment date and time be solidified)
    * Connections: until the order is finalized, the customer cannot have their order fulfilled, items do not physically move from the inventory, and selected items remain unprocessed.

4. Items can be selected from the available inventory.
    * Size: 5 (key function required for basic operations)
    * Connections: the quantity of an item a customer can order is restricted by how much of that item is in inventory; customer specification that an item is substitutable allows for inventory quantity of next closes item to be accessed if needed; number of an item in an order cannot exceed the number of items in inventory.

5. Selected Items can be added to an order with specific instructions.
    * Size: 1-2 (useful but not necessarily essential)
    * Connections: the user gives these specific instructions; they are attached to specific items in the course of the "SelectedItems" action; additional customer instructions are tied to their order using the corresponding Order ID.

6. Orders are assigned a fullfill date and time after being finalized.
    * Size: 3 (key supporting detail)
    * Connections: customer can't have order fulfilled until "Finalized" is set to true, similar to user story #3; store staff will use date and time to know when to get customer's items ready for pickup.

### Potential Complications?

Though it is difficult for me to envision potential complications without attempting to implement this model first, I would think that, in practice, the quantities and locations of Foreign Keys might change, though I am not quite sure how.  There is some potential that some of the attributes that I have designated as ints might work better as strings, or that the size of some of the varchars may need to be adjusted up or down depending on what more realistic usage demands turn out to look like.