# GraphQL CRM System

A comprehensive Customer Relationship Management (CRM) system built with Django and GraphQL, featuring advanced querying capabilities, mutations, and filtering functionality.

## Overview

This project demonstrates the power of GraphQL integrated with Django to create a flexible, efficient CRM system. Unlike traditional REST APIs with multiple endpoints, this GraphQL implementation provides a single endpoint for all operations while allowing clients to request exactly the data they need.

## Features

- **GraphQL API**: Single endpoint for all operations
- **CRUD Operations**: Create, read, update, and delete customers, products, and orders
- **Advanced Filtering**: Complex filtering capabilities for all entities
- **Bulk Operations**: Bulk customer creation with partial success handling
- **Nested Queries**: Fetch related data in a single request
- **Real-time Schema Documentation**: Interactive GraphiQL interface
- **Robust Validation**: Custom error handling and validation
- **Performance Optimized**: Pagination and N+1 query prevention

## Technology Stack

- **Backend**: Django 4.x
- **GraphQL**: graphene-django
- **Database**: Django ORM (SQLite/PostgreSQL)
- **Filtering**: django-filter
- **Testing**: GraphiQL, Insomnia, Postman

## Prerequisites

- Python 3.8+
- Django 4.x
- pip (Python package installer)

## Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/alx-backend-graphql_crm.git
   cd alx-backend-graphql_crm
   ```

2. **Create virtual environment**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install django graphene-django django-filter
   ```

4. **Configure Django settings**
   ```python
   # Add to INSTALLED_APPS in settings.py
   INSTALLED_APPS = [
       'django.contrib.admin',
       'django.contrib.auth',
       'django.contrib.contenttypes',
       'django.contrib.sessions',
       'django.contrib.messages',
       'django.contrib.staticfiles',
       'graphene_django',
       'django_filters',
       'crm',
   ]

   # Add GraphQL schema configuration
   GRAPHENE = {
       'SCHEMA': 'alx_backend_graphql_crm.schema.schema'
   }
   ```

5. **Run migrations**
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   ```

6. **Start the development server**
   ```bash
   python manage.py runserver
   ```

7. **Access GraphiQL interface**
   Open your browser and navigate to: `http://localhost:8000/graphql`

## Data Models

### Customer
- `id`: Unique identifier
- `name`: Customer name (required)
- `email`: Unique email address (required)
- `phone`: Phone number (optional)
- `created_at`: Timestamp of creation

### Product
- `id`: Unique identifier
- `name`: Product name (required)
- `price`: Product price (required, positive decimal)
- `stock`: Available stock (optional, default: 0)

### Order
- `id`: Unique identifier
- `customer`: Foreign key to Customer
- `products`: Many-to-many relationship with Products
- `total_amount`: Calculated total amount
- `order_date`: Date of order creation

## GraphQL Operations

### Queries

#### Basic Queries
```graphql
# Get all customers
query {
  allCustomers {
    edges {
      node {
        id
        name
        email
        phone
        createdAt
      }
    }
  }
}

# Get single customer with orders
query {
  customer(id: "1") {
    name
    email
    orders {
      id
      totalAmount
      products {
        name
        price
      }
    }
  }
}
```

#### Filtered Queries
```graphql
# Filter customers by name and creation date
query {
  allCustomers(filter: { 
    nameIcontains: "Ali", 
    createdAtGte: "2025-01-01" 
  }) {
    edges {
      node {
        id
        name
        email
        createdAt
      }
    }
  }
}

# Filter products by price range and sort by stock
query {
  allProducts(filter: { 
    priceGte: 100, 
    priceLte: 1000 
  }, orderBy: "-stock") {
    edges {
      node {
        id
        name
        price
        stock
      }
    }
  }
}

# Filter orders by customer and product details
query {
  allOrders(filter: { 
    customerName: "Alice", 
    productName: "Laptop", 
    totalAmountGte: 500 
  }) {
    edges {
      node {
        id
        customer { name }
        products { name }
        totalAmount
        orderDate
      }
    }
  }
}
```

### Mutations

#### Create Customer
```graphql
mutation {
  createCustomer(input: {
    name: "Alice Johnson",
    email: "alice@example.com",
    phone: "+1234567890"
  }) {
    customer {
      id
      name
      email
      phone
    }
    message
  }
}
```

#### Bulk Create Customers
```graphql
mutation {
  bulkCreateCustomers(input: [
    { 
      name: "Bob Smith", 
      email: "bob@example.com", 
      phone: "123-456-7890" 
    },
    { 
      name: "Carol White", 
      email: "carol@example.com" 
    }
  ]) {
    customers {
      id
      name
      email
    }
    errors
  }
}
```

#### Create Product
```graphql
mutation {
  createProduct(input: {
    name: "Gaming Laptop",
    price: 1299.99,
    stock: 15
  }) {
    product {
      id
      name
      price
      stock
    }
  }
}
```

#### Create Order
```graphql
mutation {
  createOrder(input: {
    customerId: "1",
    productIds: ["1", "2"]
  }) {
    order {
      id
      customer { name }
      products {
        name
        price
      }
      totalAmount
      orderDate
    }
  }
}
```

## 🔧 Available Filters

### Customer Filters
- `name`: Case-insensitive partial match
- `email`: Case-insensitive partial match
- `created_at__gte`: Created after date
- `created_at__lte`: Created before date
- `phone_pattern`: Custom phone pattern matching

### Product Filters
- `name`: Case-insensitive partial match
- `price__gte`: Minimum price
- `price__lte`: Maximum price
- `stock__gte`: Minimum stock
- `stock__lte`: Maximum stock

### Order Filters
- `total_amount__gte`: Minimum total amount
- `total_amount__lte`: Maximum total amount
- `order_date__gte`: Orders after date
- `order_date__lte`: Orders before date
- `customer_name`: Filter by customer name
- `product_name`: Filter by product name

## Best Practices Implemented

| Area | Implementation |
|------|----------------|
| **Schema Design** | Clean, modular schema with reusable types and clear naming |
| **Security** | Authentication and authorization in resolvers |
| **Error Handling** | Custom error messages and graceful exception handling |
| **Pagination** | Implemented on large query sets for performance |
| **N+1 Problem** | Optimized queries to prevent N+1 issues |
| **Testing** | Comprehensive testing with GraphiQL |
| **Documentation** | Automatic schema documentation via GraphiQL |

## Testing

### Using GraphiQL
1. Navigate to `http://localhost:8000/graphql`
2. Use the interactive interface to test queries and mutations
3. Access automatic documentation via the "Docs" panel

### Using External Tools
- **Insomnia**: Import GraphQL endpoint for advanced testing
- **Postman**: Use GraphQL collections for API testing

## Real-World Use Cases

This CRM system is ideal for:
- **E-commerce platforms** requiring flexible product and order management
- **Service businesses** needing customer relationship tracking
- **Dashboard applications** requiring real-time, precise data queries
- **Mobile applications** with limited bandwidth needs
- **Multi-tenant systems** requiring different data views per client

## Project Structure

```
alx-backend-graphql_crm/
├── alx_backend_graphql_crm/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── schema.py
├── crm/
│   ├── __init__.py
│   ├── models.py
│   ├── schema.py
│   ├── filters.py
│   ├── admin.py
│   └── apps.py
├── manage.py
├── requirements.txt
└── README.md
```

## API Documentation

The GraphQL schema is self-documenting. Access the interactive documentation at:
- **GraphiQL Interface**: `http://localhost:8000/graphql`
- **Schema Introspection**: Available through any GraphQL client

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- **Facebook/Meta** for developing GraphQL
- **Django Software Foundation** for the Django framework
- **GraphQL Foundation** for maintaining GraphQL specifications
- **ALX Africa** for the learning opportunity

## Support

For questions or support, please:
1. Check the [Issues](../../issues) page
2. Create a new issue with detailed information
3. Contact the development team

---


