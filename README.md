# Reto-7
# Reto 6 POO
### Katherine Restrepo

##### The restaurant class revisted like for the third time.
##### Add the proper data structure to manage multiple orders (maybe a FIFO queue)
##### Define a named tuple somewhere in the menu, e.g. to define a set of items.
##### Create an interface in the order class, to create a new menu, aggregate the functions for add, update, delete items. All the menus should be stored as JSON files. (use dicts for this task.)

```python
import json
from collections import deque, namedtuple
from typing import Dict

EntradaMenu = namedtuple("EntradaMenu", ["nombre", "precio"])


class ElementoMenu:
    def __init__(self, nombre: str, precio: float):
        self.nombre = nombre
        self.precio = precio

    def calcular_precio_total(self, cantidad: int = 1):
        return self.precio * cantidad


class Bebida(ElementoMenu):
    def __init__(self, nombre, precio, con_azucar: bool = False):
        super().__init__(nombre, precio)
        self._con_azucar = con_azucar

    def obtener_con_azucar(self):
        return self._con_azucar

    def establecer_con_azucar(self, valor):
        self._con_azucar = valor


class Entrada(ElementoMenu):
    def __init__(self, nombre, precio, con_sal: bool = False):
        super().__init__(nombre, precio)
        self._con_sal = con_sal

    def obtener_con_sal(self):
        return self._con_sal

    def establecer_con_sal(self, valor):
        self._con_sal = valor


class PlatoPrincipal(ElementoMenu):
    def __init__(self, nombre, precio, vegano: bool = False):
        super().__init__(nombre, precio)
        self._vegano = vegano

    def obtener_vegano(self):
        return self._vegano

    def establecer_vegano(self, valor):
        self._vegano = valor



class Orden:
    cola_ordenes = deque()

    def __init__(self):
        self.items = []

    def agregar_item(self, item: ElementoMenu):
        self.items.append(item)

    def calcular_total(self, descuento=0):
        total_bebidas = sum(item.calcular_precio_total() for item in self.items if isinstance(item, Bebida))
        total_entradas = sum(item.calcular_precio_total() for item in self.items if isinstance(item, Entrada))
        total_platos = sum(item.calcular_precio_total() for item in self.items if isinstance(item, PlatoPrincipal))

        total = total_bebidas + total_entradas + total_platos

        if total_entradas > 0:
            total_bebidas *= 0.9
            total = total_bebidas + total_entradas + total_platos
        elif descuento > 0:
            total -= total * descuento / 100

        return round(total, 2)

   
    def agregar_orden(orden):
        Orden.cola_ordenes.append(orden)


    def procesar_siguiente_orden():
        if Orden.cola_ordenes:
            return Orden.cola_ordenes.popleft()
        else:
            print("No hay órdenes pendientes.")
            return None


    def crear_menu(diccionario_menu: Dict[str, float], archivo="menu.json"):
        with open(archivo, "w") as f:
            json.dump(diccionario_menu, f)

    def agregar_item_menu(nombre: str, precio: float, archivo="menu.json"):
        try:
            with open(archivo, "r") as f:
                menu = json.load(f)
        except FileNotFoundError:
            menu = {}

        menu[nombre] = precio
        with open(archivo, "w") as f:
            json.dump(menu, f)

    def eliminar_item_menu(nombre: str, archivo="menu.json"):
        with open(archivo, "r") as f:
            menu = json.load(f)

        if nombre in menu:
            del menu[nombre]
            with open(archivo, "w") as f:
                json.dump(menu, f)
        else:
            print("Item no encontrado en el menú.")

    def actualizar_item_menu(nombre: str, nuevo_precio: float, archivo="menu.json"):
        with open(archivo, "r") as f:
            menu = json.load(f)

        if nombre in menu:
            menu[nombre] = nuevo_precio
            with open(archivo, "w") as f:
                json.dump(menu, f)
        else:
            print("Item no encontrado para actualizar.")


class Pago:
    def pagar(self, monto):
        raise NotImplementedError("Debes implementar este método")


class Tarjeta(Pago):
    def __init__(self, numero, cvv):
        self.numero = numero
        self.cvv = cvv

    def pagar(self, monto):
        print(f"Pagando {monto} con tarjeta terminada en {self.numero[-4:]}")


class Efectivo(Pago):
    def __init__(self, monto_entregado):
        self.monto_entregado = monto_entregado

    def pagar(self, monto):
        if self.monto_entregado >= monto:
            print(f"Pago en efectivo exitoso. Cambio: {self.monto_entregado - monto}")
        else:
            print(f"Fondos insuficientes. Faltan {monto - self.monto_entregado}")

```
