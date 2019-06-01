# Rating Platform Design

## Assumptions
1. Customer is not allowed to modify his product rating. Also, there might be certain cases where a user is not allowed to rate a product which he has rented. e.g. customer has returned the product too soon or the order is very old. I've added support to handle this.
2. I have only focussed on the rating platform. This leads to different design. e.g. I've created a separate endpoint to fetch the ratings for a product. But for the overall platform, it will make more sense to include ratings within the product details.
3. Customer can only rate in full points i.e. he can't rate 1.5/5.

## Architecture
![Architecture](architecture.png?raw=true "Title")

## Entities
![Entities](db.png?raw=true "Title")


## Capture Rating

1. Customer opens the order history page and sees the products that he's rented.
2. For each product, `client JS` will send a `GET /rating/check_eligibility` with the following JSON (this is required to support Assumption-1):
	```
	{
	  'product_id':
	  'customer_id':
	}
	```
	On backend, `RatingController` handles this request and makes a call to `RatingService.isUserEligibleToRate()` method. `RatingController` will in turn respond with `{'is_eligible': true/false }`
	
	If `true`, `Web JS` will give the customer an option to rate the product.
3. Customer adds his rating and clicks on submit.
3. `client JS` sends `POST /rating` request to the server with the following JSON payload:
	```
	{
	  'product_id':
	  'rating':
	  'customer_id':
	}
	```
	On backend, `RatingController` handles this request and makes a call to `RatingService.addRating()` method which adds a new row into `ProductRating` table in the database.


## Display (Average) Ratings

1. Customer opens the product page and clicks on 'get rating' button.
2. `Web JS` makes a `GET /rating?product_id={}` to fetch the rating. 

	On backend, `RatingController` handles this request and makes a call to `RatingService.getRating()` method which fetches the ratings from `ProductRating` table of the database, computes average and sends the response `{'rating': 4.3}` to `Web JS`.

3. Rating will be displayed.

## `RatingService` Interface

```
interface RatingService{

	/**
	 * Retrieves rating of product by product id.
	 *
	 * @param productId must be valid.
	 * @throws ProductNotFoundException if {@code productId} not found.
	 * @return product rating with the given productId or {@literal Optional#empty()} if none found
	 */
	Optional<T> getRating(long productId);

	/**
	 * Add rating of product with given customerId and productId
	 *
	 * @param customerId must be valid.
	 * @param productId must be valid.
	 * @throws CustomerNotFoundException if {@code customerId} not found.
	 * @throws ProductNotFoundException if {@code productId} not found.
	 */
	void addRating(long customerId, long productId, int rating);

	/**
	 * Returns whether a customer with given customerId and productId is eligible to rate.
	 *
	 * @param customerId must exist in User table.
	 * @param productId must exist in Product table.
	 * @return {@literal true} if customer is eligible to rate or {@literal false} otherwise.
	 * @throws CustomerNotFoundException if {@code customerId} not found.
	 * @throws ProductNotFoundException if {@code productId} not found.
	 */
	boolean isUserEligibleToRate(long customerId, long productId);
}
```

