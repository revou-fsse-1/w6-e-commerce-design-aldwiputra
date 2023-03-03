# Ecommerce Design Aldiansyah

## :arrow_up: High Level Design

![system-design-diagram](/assets/system-design.png)

The image above shows a high level overview of how this ecommerce app structured. I went with the **MVC (Model View Controller)** architecture. The first reason behind my decision to even use an architectural pattern is to abide by the **Single Responsibility** principle within **(S)OLID**. This separation of concerns makes this application easier to scale and maintain. Here are some benefits of using MVC for a Web Application quoted from [www.devteam.space](https://www.devteam.space/blog/how-to-build-mvc-web-app/):

- Ability to separate business logic and presentation.
- Ability to change model and view independently.
- Increase of code reusing (using software components more than once).
- Ability to display data from one model in different views.
- Ability to handle the same user action differently depending on the context.
- Increase the speed of application development due to the fact that models, controllers, and views can be developed independently of each other.

## :shopping_cart: Create Order

Below is a Sequence Diagram I created to give you an idea of how the create order functionality is implemented. There is an assumption made that the database's create-order operation is done by adding the new entry to the very last order, and the user ids are assigned incrementally and the database is sorted by it.

![create-order-diagram](/assets/create-order-diagram.png)

For a more detailed implementation I have provided a pseudocode below.

```
    function form_handler():
      let shippingDetails = getShippingDetails()

      sendRequest(endpoint, shippingDetails)


    function createOrderController(req):
      let userId = req.userId
      let shippingDetails = req.shippingDetails
      let model = Model()

      if (model.checkUserExistence(userId)):
        let total = model.calculateTotalItemsInCart()
        let payment = callPaymentGatewayAPI(total)

        if (payment.isSuccess):
          model.updateStock()
          return order created successfully

      return user doesn't exist


    class Model:
      method checkUserExistence(userId):
        for user in users database:
          if userId == i.userId
            return true

        return false


      method updateStock():
        for product in products bought by this user:
          for product in products database:
            if product bought == product from database
              product.stock -= product bought quantity
```

Most of the operations within the createOrderController are constants except for model.checkUserExistence() and model.updateStock(). So:

- model.checkUserExistence() uses a loop to find the user therefore the complexity of this operation is **O($n$)**.
- model.updateStock() runs an outer loop that loops over total products bought (let's call it $n$), and inside is another loop that loops over all products in database (let's call it $m$). So, the calculation of both loops' time complexity is O($nm$). Since $n <= m$, and the worst input that $n$ can get is when it's equal to $m$, we can conclude that the time complexity of this operation is O($n^2$).
