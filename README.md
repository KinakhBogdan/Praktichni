[laba3.py](https://github.com/user-attachments/files/34146255/laba3.py)
from typing import List, Dict


class Product:
    def __init__(self, product_id: int, name: str, price: float, stock: int):
        self.product_id = product_id
        self.name = name
        self.price = price
        self.stock = stock

    def __repr__(self):
        return f"[{self.product_id}] {self.name:<20} | Ціна: {self.price:.2f}грн | Залишок: {self.stock} шт."


class CartItem:
    def __init__(self, product: Product, quantity: int):
        self.product = product
        self.quantity = quantity

    get_total = lambda self: self.product.price * self.quantity

    def __repr__(self):
        return f"{self.product.name:<20} x {self.quantity:<3} = {self.get_total():.2f}грн"


class Store:
    def __init__(self):
        self.catalog: Dict[int, Product] = {
            1: Product(1, "Ноутбук", 25000.0, 5),
            2: Product(2, "Мишка", 450.5, 15),
            3: Product(3, "Клавіатура", 1200.0, 8),
            4: Product(4, "Монітор", 7800.99, 3),
            5: Product(5, "Навушники", 950.0, 10),
        }
        self.cart: List[CartItem] = []
        self._admin_pass = "admin123"

    format_price = lambda self, price: f"{price:.2f}грн"
    calculate_cart_total = lambda self: sum(map(lambda item: item.get_total(), self.cart))

    def show_catalog(self):
        print("\n=== КАТАЛОГ ТОВАРІВ ===")
        available_products = list(filter(lambda p: p.stock > 0, self.catalog.values()))
        if not available_products:
            print("Наразі немає товарів у наявності.")
            return

        for product in available_products:
            print(product)

    def add_to_cart(self):
        self.show_catalog()
        try:
            prod_id = int(input("\nВведіть ID товару для додавання: "))
            if prod_id not in self.catalog:
                print("❌ Товару з таким ID не існує!")
                return

            product = self.catalog[prod_id]
            qty = int(input(f"Введіть кількість (доступно: {product.stock}): "))

            if qty <= 0:
                print("❌ Кількість має бути більше 0.")
                return
            if qty > product.stock:
                print("❌ Недостатньо товару на складі!")
                return

            existing_item = next((item for item in self.cart if item.product.product_id == prod_id), None)
            if existing_item:
                if existing_item.quantity + qty > product.stock:
                    print("❌ Сумарна кількість у кошику перевищує залишок на складі!")
                    return
                existing_item.quantity += qty
            else:
                self.cart.append(CartItem(product, qty))

            print(f"✅ Додано {qty} шт. '{product.name}' до кошика.")
        except ValueError:
            print("❌ Помилка введення! Вводьте лише числа.")

    def show_cart(self):
        print("\n=== ВАШ КОШИК ===")
        if not self.cart:
            print("Кошик порожній.")
            return

        for idx, item in enumerate(self.cart, 1):
            print(f"{idx}. {item}")

        print("-" * 35)
        print(f"Загальна сума: {self.format_price(self.calculate_cart_total())}")

    def remove_from_cart(self):
        if not self.cart:
            print("\n❌ Кошик порожній, нічого видаляти.")
            return

        self.show_cart()
        try:
            choice = int(input("\nВведіть номер позиції для видалення: "))
            if 1 <= choice <= len(self.cart):
                removed = self.cart.pop(choice - 1)
                print(f"✅ Позицію '{removed.product.name}' видалено з кошика.")
            else:
                print("❌ Некоректний номер позиції!")
        except ValueError:
            print("❌ Помилка введення! Вводьте лише числа.")

    def checkout(self):
        if not self.cart:
            print("\n❌ Кошик порожній. Додайте товари перед покупкою.")
            return

        self.show_cart()
        confirm = input("\nПідтвердити покупку? (так/ні): ").strip().lower()
        if confirm in ["так", "yes", "y", "1"]:
            for item in self.cart:
                item.product.stock -= item.quantity

            total = self.calculate_cart_total()
            self.cart.clear()
            print(f"\n🎉 Дякуємо за покупку! Сплачено: {self.format_price(total)}")
        else:
            print("Покупку скасовано.")

    def admin_panel(self):
        password = input("\nВведіть пароль адміністратора: ")
        if password != self._admin_pass:
            print("❌ Невірний пароль!")
            return

        print("\n=== ПАНЕЛЬ АДМІНІСТРАТОРА (ЗАЛИШКИ НА СКЛАДІ) ===")
        sorted_catalog = sorted(self.catalog.values(), key=lambda p: p.stock)

        for product in sorted_catalog:
            status = "🔴 КРИТИЧНО" if product.stock < 5 else "🟢 В НАЯВНОСТІ"
            print(f"ID: {product.product_id:<2} | {product.name:<20} | Залишок: {product.stock:<3} шт. | {status}")


def main():
    store = Store()

    while True:
        print("\n" + "=" * 30)
        print("    ГОЛОВНЕ МЕНЮ МАГАЗИНУ")
        print("=" * 30)
        print("1. Переглянути каталог")
        print("2. Додати товар у кошик")
        print("3. Переглянути кошик")
        print("4. Видалити товар з кошика")
        print("5. Купити товари (Оформити замовлення)")
        print("6. Увійти як адміністратор")
        print("0. Вийти")

        choice = input("Оберіть пункт меню (0-6): ").strip()

        if choice == "1":
            store.show_catalog()
        elif choice == "2":
            store.add_to_cart()
        elif choice == "3":
            store.show_cart()
        elif choice == "4":
            store.remove_from_cart()
        elif choice == "5":
            store.checkout()
        elif choice == "6":
            store.admin_panel()
        elif choice == "0":
            print("\nДо побачення!")
            break
        else:
            print("❌ Некоректний вибір! Спробуйте ще раз.")


if __name__ == "__main__":
    main()
