# Solutions

### Challenges

**Controller Tests with Recipes!**

1. Create a `Recipe` model and its controller. A recipe should include the dish's title and the instructions for making the dish. You can assume the instructions are plain text.

  ```
  $ rails g model Recipe title instructions
  $ rails g controller recipes
  ```

1. Write the spec for `recipes#index` (`recipes` controller, `index` action). It should render an index view with data from all existing recipes in the database. Do you expect your tests to pass or fail? Run the spec.

  ```ruby
  #
  # spec/controllers/recipes_controller_spec.rb
  #
  RSpec.describe RecipesController, type: :controller do
    describe "#index" do
      before do
        get :index
      end

      it "should assign @recipes" do
        all_recipes = Recipe.all
        expect(assigns(:recipes)).to eq(all_recipes)
      end

      it "should render the :index view" do
        expect(response).to render_template(:index)
      end
    end

  end
  ```

  ```
  # to run all tests
  $ rspec
  ```

1. Update your controller to fill in the `index` action, and make sure it passes the tests you wrote.

  ```ruby
  #
  # app/controllers/recipes_controller.rb
  #
  class RecipesController < ApplicationController

    def index
      @recipes = Recipe.all
      render :index
    end

  end
  ```

  ```
  # to run all tests
  $ rspec
  ```

1. Write the spec for a `new` action. It should render a new view (which would have the new recipe form). Do you expect your tests to pass or fail? Run the tests.

  ```ruby
  #
  # spec/controllers/recipes_controller_spec.rb
  #
  RSpec.describe RecipesController, type: :controller do

    ...

    describe "#new" do
      before do
        get :new
      end

      it "should assign @recipe" do
        expect(assigns(:recipe)).to be_instance_of(Recipe)
      end

      it "should render the :new view" do
        expect(response).to render_template(:new)
      end
    end

  end
  ```

1. Update your controller to fill in the `new` action, and pass the tests in your spec.

  ```ruby
  #
  # app/controllers/recipes_controller.rb
  #
  class RecipesController < ApplicationController

    ...

    def new
      @recipe = Recipe.new
      render :new
    end

  end
  ```

1. Write a spec for a `create` action. It should use data from parameters to add a recipe to the database, then redirect to a show view for the new recipe. Do you expect your tests to pass?

  ```ruby
  #
  # spec/controllers/recipes_controller_spec.rb
  #
  RSpec.describe RecipesController, type: :controller do

    ...

    describe "#create" do
      context "success" do
        before do
          @recipes_count = Recipe.count
          post :create, recipe: { title: FFaker::Lorem.words(2).join(" "), instructions: FFaker::Lorem.sentence }
        end

        it "should add new recipe to current user" do
          expect(Recipe.count).to eq(@recipes_count + 1)
        end

        it "should redirect_to 'recipe_path' after successful create" do
          expect(response.status).to be(302)
          expect(response.location).to match(/\/recipes\/\d+/)
        end
      end

      context "failure" do
        it "should redirect_to 'new_recipe_path' when create fails" do
          # create blank recipe (fails validations)
          post :create, recipe: { title: nil, instructions: nil }
          expect(response).to redirect_to(new_recipe_path)
        end
      end
    end

  end
  ```

1. Update your controller to fill in the `create` action, and pass as many of the tests you wrote as possible so far. **Hint:** Don't write a `show` action for this step!

  ```ruby
  #
  # app/controllers/recipes_controller.rb
  #
  class RecipesController < ApplicationController

    ...

    def create
      recipe = Recipe.new(recipe_params)
      if recipe.save
        redirect_to recipe_path(recipe)
      else
        redirect_to new_recipe_path
      end
    end

  end
  ```

1. Write a spec for a `show` action. It should render a show view with information for a single recipe. It will be associated with a parameterized url (as you can see in `rake routes`).  **Hint:** How will you get the `id` to use in the test?

  ```ruby
  #
  # spec/controllers/recipes_controller_spec.rb
  #
  RSpec.describe RecipesController, type: :controller do

    ...

    describe "#show" do
      before do
        @recipe = Recipe.create(title: FFaker::Lorem.words(2).join(" "), instructions: FFaker::Lorem.sentence)
        get :show, id: @recipe.id
      end

      it "should assign @recipe" do
        expect(assigns(:recipe)).to eq(@recipe)
      end

      it "should render the :show view" do
        expect(response).to render_template(:show)
      end
    end

  end
  ```

1. Update your controller to pass the tests you wrote for your `show` action.

  ```ruby
  #
  # app/controllers/recipes_controller.rb
  #
  class RecipesController < ApplicationController

    ...

    def show
      @recipe = Recipe.find(params[:id])
      render :show
    end

  end
  ```

1. At this point, make sure all the tests you wrote for your `create` action are passing as well.

  ```
  # to run all tests
  $ rspec
  ```

### Stretch Challenges

1. Write a spec for an `edit` action. It should render the edit view (which shows the edit recipe form). The `edit` action is associated with a parameterized url. Since we want to display the current version of the recipe within the form, the `edit` action will use the id from the url to get that information from the database and make it available in the edit view.

  ```ruby
  #
  # spec/controllers/recipes_controller_spec.rb
  #
  RSpec.describe RecipesController, type: :controller do

    ...

    describe "#edit" do
      before do
        @recipe = Recipe.create(title: FFaker::Lorem.words(2).join(" "), instructions: FFaker::Lorem.sentence)
        get :edit, id: @recipe.id
      end

      it "should assign @recipe" do
        expect(assigns(:recipe)).to eq(@recipe)
      end

      it "should render the :edit view" do
        expect(response).to render_template(:edit)
      end
    end

  end
  ```

1. Update your controller to pass the tests you wrote for your `edit` action.

  ```ruby
  #
  # app/controllers/recipes_controller.rb
  #
  class RecipesController < ApplicationController

    ...

    def edit
      @recipe = Recipe.find(params[:id])
      render :edit
    end

  end
  ```

1. Write a spec for an `update` action. It should take in new data for a specific recipe, change the recipe in the database, and redirect to the show page for the item.

  ```ruby
  #
  # spec/controllers/recipes_controller_spec.rb
  #
  RSpec.describe RecipesController, type: :controller do

    ...

    describe "#update" do
      before do
        @recipe = Recipe.create(title: FFaker::Lorem.words(2).join(" "), instructions: FFaker::Lorem.sentence)
      end

      context "success" do
        before do
          @new_title = FFaker::Lorem.words(3).join(" ")
          @new_instructions = FFaker::Lorem.sentence
          put :update, id: @recipe.id, recipe: { title: @new_title, instructions: @new_instructions }
          # reload @recipe to get changes from :update
          @recipe.reload
        end

        it "should update the recipe in the database" do
          expect(@recipe.title).to eq(@new_title)
          expect(@recipe.instructions).to eq(@new_instructions)
        end

        it "should redirect_to 'recipe_path' after successful update" do
          expect(response.status).to be(302)
          expect(response.location).to match(/\/recipes\/\d+/)
        end
      end

      context "failure" do
        it "should redirect_to 'edit_recipe_path' when update fails" do
          # update with blank recipe params (fails validations)
          put :update, id: @recipe.id, recipe: { title: nil, instructions: nil }
          expect(response).to redirect_to(edit_recipe_path)
        end
      end
    end

  end
  ```

1. Update your controller to pass the tests you wrote for your `update` action.

  ```ruby
  #
  # app/controllers/recipes_controller.rb
  #
  class RecipesController < ApplicationController

    ...

    def update
      recipe = Recipe.find(params[:id])
      if recipe.update_attributes(recipe_params)
        redirect_to recipe_path(recipe)
      else
        redirect_to edit_recipe_path
      end
    end

  end
  ```

1. Refactor the `create` method in your `RecipesController` to pass your new `create` action tests.

  ```ruby
  #
  # app/controllers/recipes_controller.rb
  #
  class RecipesController < ApplicationController

    ...

    def create
      recipe = current_user.recipes.new(recipe_params)
      if recipe.save
        redirect_to recipe_path(recipe)
      else
        redirect_to new_recipe_path
      end
    end

  end
  ```
