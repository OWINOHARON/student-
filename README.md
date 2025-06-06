import React, { useState } from "react";

const initialProducts = [
  { id: 1, name: "Soap", price: 2.5, stock: 20 },
  { id: 2, name: "Toothpaste", price: 3.0, stock: 15 },
  { id: 3, name: "Sugar (1kg)", price: 1.8, stock: 30 },
];

export default function POS() {
  const [products, setProducts] = useState(initialProducts);
  const [cart, setCart] = useState([]);
  const [salesHistory, setSalesHistory] = useState([]);

  const addToCart = (product) => {
    const existing = cart.find((item) => item.id === product.id);
    if (existing) {
      setCart(
        cart.map((item) =>
          item.id === product.id && item.quantity < product.stock
            ? { ...item, quantity: item.quantity + 1 }
            : item
        )
      );
    } else {
      setCart([...cart, { ...product, quantity: 1 }]);
    }
  };

  const removeFromCart = (id) => {
    setCart(cart.filter((item) => item.id !== id));
  };

  const getTotal = () => {
    return cart.reduce((sum, item) => sum + item.price * item.quantity, 0);
  };

  const completeSale = () => {
    const updatedProducts = products.map((product) => {
      const cartItem = cart.find((item) => item.id === product.id);
      if (cartItem) {
        return { ...product, stock: product.stock - cartItem.quantity };
      }
      return product;
    });
    setProducts(updatedProducts);
    setSalesHistory([
      ...salesHistory,
      { cart, total: getTotal(), time: new Date() },
    ]);
    setCart([]);
  };

  return (
    <div className="p-4 max-w-screen-md mx-auto mt-8 bg-white rounded shadow">
      <div className="mb-6">
        <h2 className="text-2xl font-bold mb-4">Product List</h2>
        {products.map((product) => (
          <div
            key={product.id}
            className="flex justify-between items-center border-b py-2"
          >
            <span>
              {product.name} (${product.price}) - Stock: {product.stock}
            </span>
            <button
              className="bg-blue-500 text-white px-3 py-1 rounded disabled:opacity-50"
              onClick={() => addToCart(product)}
              disabled={product.stock === 0}
            >
              Add
            </button>
          </div>
        ))}
      </div>

      <div>
        <h2 className="text-2xl font-bold mb-4">Cart</h2>
        {cart.length === 0 ? (
          <p className="text-gray-500">Your cart is empty.</p>
        ) : (
          cart.map((item) => (
            <div
              key={item.id}
              className="flex justify-between items-center border-b py-2"
            >
              <span>
                {item.name} x {item.quantity} = ${(
                  item.price * item.quantity
                ).toFixed(2)}
              </span>
              <button
                className="bg-red-500 text-white px-3 py-1 rounded"
                onClick={() => removeFromCart(item.id)}
              >
                Remove
              </button>
            </div>
          ))
        )}

        <div className="text-lg font-semibold mt-4">
          Total: ${getTotal().toFixed(2)}
        </div>
        <button
          className="bg-green-600 text-white px-4 py-2 rounded mt-3 w-full disabled:opacity-50"
          onClick={completeSale}
          disabled={cart.length === 0}
        >
          Complete Sale
        </button>
      </div>

      {salesHistory.length > 0 && (
        <div className="mt-8">
          <h2 className="text-2xl font-bold mb-2">Sales History</h2>
          <ul className="space-y-2">
            {salesHistory.map((sale, idx) => (
              <li key={idx} className="border p-2 rounded">
                <div>
                  <span className="font-semibold">Time:</span>{" "}
                  {sale.time.toLocaleString()}
                </div>
                <div>
                  <span className="font-semibold">Items:</span>{" "}
                  {sale.cart.map((item) => `${item.name} x${item.quantity}`).join(", ")}
                </div>
                <div>
                  <span className="font-semibold">Total:</span> $
                  {sale.total.toFixed(2)}
                </div>
              </li>
            ))}
          </ul>
        </div>
      )}
    </div>
  );
}# student-