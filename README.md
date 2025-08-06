<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Simple eCommerce Page</title>
<style>
  * {
    box-sizing: border-box;
  }
  body {
    font-family: Arial, sans-serif;
    margin: 0; padding: 0;
    background: #f5f5f5;
  }
  header {
    background: #007bff;
    color: white;
    padding: 1rem 2rem;
    text-align: center;
  }
  main {
    display: flex;
    flex-wrap: wrap;
    padding: 2rem;
    gap: 2rem;
    justify-content: center;
  }
  .product {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 6px rgb(0 0 0 / 0.1);
    width: 220px;
    padding: 1rem;
    text-align: center;
  }
  .product img {
    max-width: 100%;
    border-radius: 8px;
    height: 150px;
    object-fit: cover;
  }
  .product h3 {
    margin: 0.5rem 0;
    font-size: 1.1rem;
  }
  .product .price {
    font-weight: bold;
    margin-bottom: 0.5rem;
    color: #28a745;
  }
  .product button {
    background: #007bff;
    color: white;
    border: none;
    padding: 0.5rem 1rem;
    border-radius: 6px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  .product button:hover {
    background: #0056b3;
  }

  /* Cart styles */
  aside.cart {
    position: fixed;
    top: 80px;
    right: 20px;
    width: 300px;
    max-height: 80vh;
    background: white;
    border-radius: 8px;
    box-shadow: 0 3px 10px rgb(0 0 0 / 0.2);
    padding: 1rem;
    overflow-y: auto;
  }
  aside.cart h2 {
    margin-top: 0;
    font-size: 1.3rem;
    border-bottom: 1px solid #ddd;
    padding-bottom: 0.5rem;
  }
  .cart-item {
    display: flex;
    justify-content: space-between;
    margin-bottom: 1rem;
    align-items: center;
  }
  .cart-item span {
    flex: 1;
  }
  .cart-item button {
    background: #dc3545;
    border: none;
    color: white;
    padding: 0.3rem 0.6rem;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.9rem;
  }
  .cart-total {
    font-weight: bold;
    text-align: right;
    margin-top: 1rem;
    font-size: 1.1rem;
  }

  /* Responsive */
  @media (max-width: 768px) {
    main {
      flex-direction: column;
      align-items: center;
    }
    aside.cart {
      position: static;
      width: 90%;
      max-height: none;
      margin: 1rem auto;
    }
  }
</style>
</head>
<body>

<header>
  <h1>Simple eCommerce Store</h1>
</header>

<main id="product-list">
  <!-- Products will be inserted here dynamically -->
</main>

<aside class="cart" id="cart">
  <h2>Shopping Cart</h2>
  <div id="cart-items">
    <p>Your cart is empty.</p>
  </div>
  <div class="cart-total" id="cart-total"></div>
</aside>

<script>
  const products = [
    {
      id: 1,
      name: "Wireless Headphones",
      price: 59.99,
      image: "https://images.unsplash.com/photo-1511367461989-f85a21fda167?auto=format&fit=crop&w=400&q=80"
    },
    {
      id: 2,
      name: "Smart Watch",
      price: 129.99,
      image: "https://images.unsplash.com/photo-1523275335684-37898b6baf30?auto=format&fit=crop&w=400&q=80"
    },
    {
      id: 3,
      name: "DSLR Camera",
      price: 499.99,
      image: "https://images.unsplash.com/photo-1506744038136-46273834b3fb?auto=format&fit=crop&w=400&q=80"
    },
    {
      id: 4,
      name: "Sneakers",
      price: 79.99,
      image: "https://images.unsplash.com/photo-1526170375885-4d8ecf77b99f?auto=format&fit=crop&w=400&q=80"
    }
  ];

  const productListEl = document.getElementById("product-list");
  const cartItemsEl = document.getElementById("cart-items");
  const cartTotalEl = document.getElementById("cart-total");

  let cart = [];

  function formatPrice(price) {
    return "$" + price.toFixed(2);
  }

  function renderProducts() {
    productListEl.innerHTML = "";
    products.forEach(product => {
      const productEl = document.createElement("div");
      productEl.className = "product";
      productEl.innerHTML = `
        <img src="${product.image}" alt="${product.name}" />
        <h3>${product.name}</h3>
        <div class="price">${formatPrice(product.price)}</div>
        <button data-id="${product.id}">Add to Cart</button>
      `;
      productListEl.appendChild(productEl);
    });
  }

  function renderCart() {
    if (cart.length === 0) {
      cartItemsEl.innerHTML = "<p>Your cart is empty.</p>";
      cartTotalEl.textContent = "";
      return;
    }

    cartItemsEl.innerHTML = "";
    cart.forEach(item => {
      const itemEl = document.createElement("div");
      itemEl.className = "cart-item";
      itemEl.innerHTML = `
        <span>${item.name} x ${item.quantity}</span>
        <span>${formatPrice(item.price * item.quantity)}</span>
        <button data-id="${item.id}">&times;</button>
      `;
      cartItemsEl.appendChild(itemEl);
    });

    const total = cart.reduce((sum, item) => sum + item.price * item.quantity, 0);
    cartTotalEl.textContent = "Total: " + formatPrice(total);
  }

  function addToCart(productId) {
    const product = products.find(p => p.id === productId);
    if (!product) return;

    const cartItem = cart.find(item => item.id === productId);
    if (cartItem) {
      cartItem.quantity++;
    } else {
      cart.push({...product, quantity: 1});
    }
    renderCart();
  }

  function removeFromCart(productId) {
    cart = cart.filter(item => item.id !== productId);
    renderCart();
  }

  productListEl.addEventListener("click", e => {
    if (e.target.tagName === "BUTTON") {
      const id = parseInt(e.target.getAttribute("data-id"));
      addToCart(id);
    }
  });

  cartItemsEl.addEventListener("click", e => {
    if (e.target.tagName === "BUTTON") {
      const id = parseInt(e.target.getAttribute("data-id"));
      removeFromCart(id);
    }
  });

  // Initial render
  renderProducts();
  renderCart();
</script>

</body>
</html>

