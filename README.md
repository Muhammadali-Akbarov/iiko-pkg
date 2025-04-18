# iiko-pkg

A Python library for the iiko.services API. This library provides a simple and easy-to-use interface for interacting with the iiko.services API.

## Features

- Authentication with automatic token refresh
- Organizations management
- Menu retrieval and management
- Order creation and management
- Delivery management
- Terminal groups management
- Customer management
- Payment types, order types, and discounts
- Address management (regions, cities, streets)
- Employee management (couriers)
- Marketing sources
- Error handling with custom exceptions
- Type hints and validation with Pydantic models
- Comprehensive test suite
- And more...

## Installation

```bash
pip install iiko-pkg
```

Or install from source:

```bash
git clone https://github.com/yourusername/iiko-pkg.git
cd iiko-pkg
pip install -e .
```

## Requirements

- Python 3.7+
- requests
- pydantic (version 2.0.0 or higher)

## API Key Setup

To use this library, you need an API key from iiko. For security reasons, it's recommended to use environment variables to store your API key instead of hardcoding it in your source code.

### Setting up environment variables

```bash
# Linux/macOS
export IIKO_API_KEY="your_api_key"

# Windows (Command Prompt)
set IIKO_API_KEY=your_api_key

# Windows (PowerShell)
$env:IIKO_API_KEY="your_api_key"
```

### Using the API key in your code

```python
import os
from iiko_pkg import IikoClient

# Get API key from environment variable
API_KEY = os.environ.get("IIKO_API_KEY", "")

# Initialize the client with your API key
client = IikoClient(api_key=API_KEY)
```

## Basic Usage

```python
import os
from iiko_pkg import IikoClient

# Get API key from environment variable
API_KEY = os.environ.get("IIKO_API_KEY", "")

# Initialize the client with your API key
client = IikoClient(api_key=API_KEY)

# Get organizations
organizations = client.get_organizations()
for org in organizations.organizations:
    print(f"Organization: {org.name} (ID: {org.id})")

# Get terminal groups for an organization
org_id = organizations.organizations[0].id
terminal_groups = client.get_terminal_groups([org_id])
print(terminal_groups)

# Get menu for an organization
menu = client.get_menu([org_id])
for product in menu.menus[0].products[:5]:  # Print first 5 products
    print(f"Product: {product.name} (Price: {product.price})")

# Create an order
order_response = client.create_order(
    organization_id=org_id,
    terminal_group_id=terminal_groups["terminalGroups"][0]["id"],
    order={
        "items": [
            {
                "productId": menu.menus[0].products[0].id,
                "amount": 1
            }
        ],
        "phone": "+1234567890",
        "customer": {
            "name": "John Doe"
        }
    }
)
print(f"Order created with ID: {order_response.order_id}")
```

## Advanced Usage

See the `examples` directory for more detailed examples.

### Error Handling

The library provides custom exceptions for different types of errors:

```python
import os
from iiko_pkg import IikoClient
from iiko_pkg.exceptions import AuthenticationError, ApiError, NetworkError, ValidationError

# Get API key from environment variable
API_KEY = os.environ.get("IIKO_API_KEY", "")

client = IikoClient(api_key=API_KEY)

try:
    organizations = client.get_organizations()
except AuthenticationError as e:
    print(f"Authentication error: {e}")
except ApiError as e:
    print(f"API error {e.status_code}: {e.error_message}")
except NetworkError as e:
    print(f"Network error: {e}")
except ValidationError as e:
    print(f"Validation error: {e}")
```

### Working with Models

All responses are returned as Pydantic models, which provide type hints and validation:

```python
import os
from iiko_pkg import IikoClient
from iiko_pkg.models import Organization, Menu, Product

# Get API key from environment variable
API_KEY = os.environ.get("IIKO_API_KEY", "")

client = IikoClient(api_key=API_KEY)

# Get organizations
response = client.get_organizations()
org: Organization = response.organizations[0]

# Access organization properties
print(f"Organization: {org.name}")
print(f"Address: {org.restaurant_address}")
print(f"Coordinates: {org.latitude}, {org.longitude}")

# Get menu
menu_response = client.get_menu([org.id])
menu: Menu = menu_response.menus[0]

# Access menu properties
print(f"Menu: {menu.name}")
print(f"Categories: {len(menu.categories)}")
print(f"Products: {len(menu.products)}")

# Access product properties
product: Product = menu.products[0]
print(f"Product: {product.name}")
print(f"Price: {product.price}")
print(f"Category: {product.category_id}")
```

## API Documentation

For more information about the iiko.services API, visit the official documentation at https://api-ru.iiko.services/

## Development

### Setup Development Environment

```bash
# Clone the repository
git clone https://github.com/yourusername/iiko-pkg.git
cd iiko-pkg

# Create a virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install development dependencies
pip install -e ".[dev]"
```

### Running Tests

```bash
python run_tests.py
```

Or using tox:

```bash
tox
```

### Code Style

This project uses:
- Black for code formatting
- isort for import sorting
- flake8 for linting
- mypy for type checking

You can run all of these with:

```bash
tox -e lint
tox -e type
```

## License

MIT

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

See [CONTRIBUTING.md](CONTRIBUTING.md) for more information.
