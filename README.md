import requests
import os
import sys
import time

# Set your Spoonacular API key here
API_KEY = a2c4eb7633bd405cbc1d625f995fb7f0

# Base URL for Spoonacular API
BASE_URL = 'https://api.spoonacular.com/recipes'

# Utility function for clear screen
def clear_screen():
    os.system('cls' if os.name == 'nt' else 'clear')

# Function for printing a decorative header
def print_header(title):
    print("=" * 60)
    print(f"{title.center(60)}")
    print("=" * 60)

# Function to fetch recipes based on ingredient
def search_recipes_by_ingredient(ingredient, number=5):
    url = f"{BASE_URL}/findByIngredients"
    params = {
        'ingredients': ingredient,
        'number': number,
        'apiKey': API_KEY
    }
    response = requests.get(url, params=params)
    if response.status_code != 200:
        print("Error fetching data from API.")
        return []
    return response.json()

# Function to fetch detailed recipe information
def get_recipe_details(recipe_id):
    url = f"{BASE_URL}/{recipe_id}/information"
    params = {
        'includeNutrition': False,
        'apiKey': API_KEY
    }
    response = requests.get(url, params=params)
    if response.status_code != 200:
        print("Error fetching recipe details.")
        return None
    return response.json()

# Function to display recipes in a formatted manner
def display_recipes(recipes):
    for idx, recipe in enumerate(recipes, 1):
        print(f"{idx}. {recipe['title']}")
    print()

# Function to display detailed recipe info
def display_recipe_details(recipe):
    clear_screen()
    print_header(recipe['title'])
    print(f"Ready in: {recipe['readyInMinutes']} minutes")
    print(f"Servings: {recipe['servings']}")
    print("\nIngredients:")
    for ingredient in recipe['extendedIngredients']:
        print(f" - {ingredient['originalString']}")
    print("\nInstructions:")
    if recipe['instructions']:
        print(recipe['instructions'])
    else:
        print("No instructions available.")
    print("\nPress Enter to return to main menu.")
    input()

# Main program loop
def main():
    while True:
        clear_screen()
        print_header("🍽️ Recipe Finder")
        print("Welcome! Find recipes based on ingredients you have.\n")
        ingredient = input("Enter an ingredient (or type 'exit' to quit): ").strip()
        if ingredient.lower() == 'exit':
            print("Goodbye! Happy cooking!")
            sys.exit()

        print("\nSearching for recipes...")
        recipes = search_recipes_by_ingredient(ingredient)

        if not recipes:
            print("No recipes found. Try another ingredient.")
            time.sleep(2)
            continue

        display_recipes(recipes)

        while True:
            choice = input("Select a recipe number for details or 'b' to go back: ").strip()
            if choice.lower() == 'b':
                break
            if choice.isdigit() and 1 <= int(choice) <= len(recipes):
                recipe_id = recipes[int(choice)-1]['id']
                recipe_details = get_recipe_details(recipe_id)
                if recipe_details:
                    display_recipe_details(recipe_details)
                break
            else:
                print("Invalid input. Please try again.")

if __name__ == "__main__":
    if API_KEY == a2c4eb7633bd405cbc1d625f995fb7f0:
        print("Please set your Spoonacular API key in the code before running.")
        sys.exit()
    main()
