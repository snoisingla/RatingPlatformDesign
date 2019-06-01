# Rating Platform Design

## Architecture
![Architecture](architecture.png?raw=true "Title")

## Entities
![Entities](db.png?raw=true "Title")

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
	 * @return {@literal true} if customer is eligible to rate. Customer is eligible if he has rented the product. {@literal false} otherwise.
	 * @throws CustomerNotFoundException if {@code customerId} not found.
	 * @throws ProductNotFoundException if {@code productId} not found.
	 */
	boolean isUserEligibleToRate(long customerId, long productId);
}
```
