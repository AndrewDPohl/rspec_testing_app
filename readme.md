# RSpec Testing App

## Challenges


**Controller Tests with Recipes!**

1. Create a `Recipe` model and its controller. A recipe should include the dish's title and the instructions for making the dish. You can assume the instructions are plain text.

1. Write the spec for `recipes#index` (`recipes` controller, `index` action). It should render an index view with data from all existing recipes in the database. Do you expect your tests to pass or fail? Run the spec.

1. Update your controller to fill in the `index` action, and make sure it passes the tests you wrote.

1. Write the spec for a `new` action. It should render a new view (which would have the new recipe form). Do you expect your tests to pass or fail? Run the tests.

1. Update your controller to fill in the `new` action, and pass the tests in your spec.

1. Write a spec for a `create` action. It should use data from parameters to add a recipe to the database, then redirect to a show view for the new recipe. Do you expect your tests to pass?

1. Update your controller to fill in the `create` action, and pass as many of the tests you wrote as possible so far. **Hint:** Don't write a `show` action for this step!

1. Write a spec for a `show` action. It should render a show view with information for a single recipe. It will be associated with a parameterized url (as you can see in `rake routes`).  **Hint:** How will you get the `id` to use in the test?

1. Update your controller to pass the tests you wrote for your `show` action.

1. At this point, make sure all the tests you wrote for your `create` action are passing as well.

### Stretch Challenges

1. Write a spec for an `edit` action. It should render the edit view (which shows the edit recipe form). The `edit` action is associated with a parameterized url. Since we want to display the current version of the recipe within the form, the `edit` action will use the id from the url to get that information from the database and make it available in the edit view.

1. Update your controller to pass the tests you wrote for your `edit` action.

1. Write a spec for an `update` action. It should take in new data for a specific recipe, change the recipe in the database, and redirect to the show page for the item.

1. Update your controller to pass the tests you wrote for your `update` action.

1. Refactor the `create` method in your `RecipesController` to pass your new `create` action tests.
