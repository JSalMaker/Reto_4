# Clase Programacion Orientada a Objetos: Reto_4
Reto de encapsulamiento, herencia y polimorfismo

En esta ocasión nos encontramos con un reto el cual ha sido el más complejo hasta el momento, este reto constaba de dos ejercicios prácticos los cuales se resolvían con los principales fundamentos o "pilares" de la programación orientada a objetos, usando principalmente herencia, encapsulamiento, composición y polimorfismo. El primer ejercicio era acerca de crear una clase padre la cual heredara atributos y métodos a las demás clases hijas, se usó el polimorfismo para coger un mismo método (el cual sale de la clase padre) esto con fin de usarlo individualmente en cada una de las clases de diferente manera (véase mas adelante en el código), se usó el encapsulamiento en atributos que eran protegidos y tendrían que ser obtenidos por medio de la función get y posteriormente cambiados por la función set(algunos).
El código:


```python

class Point:
    def __init__(self, x: int, y: int):
        self._x = x
        self._y = y

    def get_x(self): 
        return self._x
    def get_y(self): 
        return self._y
    def set_x(self, value): 
        self._x = value
    def set_y(self, value): 
        self._y = value

    def compute_distance(self, other):
        return ((self._x - other.get_x())**2 + (self._y - other.get_y())**2)**0.5

class Line:
    def __init__(self, start_point: Point, end_point: Point):
        self._start_point = start_point
        self._end_point = end_point
        self._length = self._start_point.compute_distance(self._end_point)

    def get_start_point(self): 
        return self._start_point
    def get_end_point(self): 
        return self._end_point
    def get_length(self): 
        return self._length

class Shape:
    def __init__(self, vertices: list, is_regular: bool):
        self._vertices = vertices 
        self._is_regular = is_regular
        self._edges = self._create_edges()
        self._inner_angles = self.compute_inner_angles()

    def _create_edges(self):
        edges = []
        n = len(self._vertices)
        for i in range(n):
            p1 = self._vertices[i]
            p2 = self._vertices[(i + 1) % n]
            edges.append(Line(p1, p2))
        return edges

    def get_vertices(self): 
        return self._vertices
    def get_edges(self): 
        return self._edges
    def get_is_regular(self): 
        return self._is_regular

    def compute_perimeter(self):
        total = 0
        for edge in self._edges:
            total += edge.get_length()
        return total

    def compute_area(self):
        pass

    def compute_inner_angles(self):
        return []

class Rectangle(Shape):
    def __init__(self, vertices: list):
        super().__init__(vertices, is_regular=False)

    def compute_area(self):
        return self._edges[0].get_length() * self._edges[1].get_length()
    
    def compute_inner_angles(self):
        return [90, 90, 90, 90]

class Square(Rectangle):
    def __init__(self, vertices: list):
        super().__init__(vertices)
        self._is_regular = True

class Triangle(Shape):
    def __init__(self, vertices: list):
        super().__init__(vertices, is_regular=False)

    def compute_area(self):
        s = self.compute_perimeter() / 2
        a, b, c = [e.get_length() for e in self._edges]
        return (s * (s - a) * (s - b) * (s - c))**0.5

    def compute_inner_angles(self): #ayuda para el calculo de angulos internos 
        s1 = self._edges[0].get_length()
        s2 = self._edges[1].get_length()
        s3 = self._edges[2].get_length()
        try:
            cos_A = (s2**2 + s3**2 - s1**2) / (2 * s2 * s3)
            cos_B = (s1**2 + s3**2 - s2**2) / (2 * s1 * s3)
            cos_C = (s1**2 + s2**2 - s3**2) / (2 * s1 * s2)
            return [cos_A, cos_B, cos_C] 
        except ZeroDivisionError:
            return [0, 0, 0]

class Equilateral(Triangle):
    def __init__(self, vertices: list):
        super().__init__(vertices)
        self._is_regular = True
    
    def compute_inner_angles(self):
        return [60, 60, 60]

class Isosceles(Triangle):
    def __init__(self, vertices: list):
        super().__init__(vertices)

class Scalene(Triangle):
    def __init__(self, vertices: list):
        super().__init__(vertices)

class TriRectangle(Triangle):
    def __init__(self, vertices: list):
        super().__init__(vertices)

    def compute_inner_angles(self):
        return super().compute_inner_angles()

```


El ejercicio 2 fue como una reutilización de uno de los códigos usados en el reto 3 visto anterior mente, este fue el código del restaurante. El reto pedía implementar principios del encapsulamiento al ejercicio, como podría ser el uso de los setters y getters, también el uso de otra clase llamada payment, la cual nos permite ver con qué modo de pago fue cancelado la cuenta del restaurante, esto por medio la creación de otra clase y de entregarle los valores como normalmente se haría, por último se implementó un descuento si es que compra plato fuerte, este descuento se aplica a las bebidas en este caso como se va a ver en el ejercicio a continuación.


```python
class MenuItem:
    def __init__(self, nombre: str, precio: int):
        self._nombre = nombre
        self._precio = precio
    def get_nombre(self): 
        return self._nombre
    def set_nombre(self, nombre): 
        self._nombre = nombre
    def get_precio(self): 
        return self._precio
    def set_precio(self, precio): 
        self._precio = precio
    def CalcularTotal(self, cantidad=1):
        return self._precio * cantidad

class Bebida(MenuItem):
    def __init__(self, nombre: str, precio: int, marca: str, tamaño: str):
        super().__init__(nombre, precio)
        self._marca = marca
        self._tamaño = tamaño
    def get_marca(self): 
        return self._marca
    def set_marca(self, m): 
        self._marca = m
    def get_tamaño(self): 
        return self._tamaño
    def set_tamaño(self, t): 
        self._tamaño = t

class Aperitivo(MenuItem):
    def __init__(self, nombre: str, precio: int, porcion=1):
        super().__init__(nombre, precio)
        self._porcion = porcion
    def get_porcion(self): 
        return self._porcion
    def set_porcion(self, p): 
        self._porcion = p

class PlatoPrincipal(MenuItem):
    def __init__(self, nombre: str, precio: int, Proteina: str, Granos: str, Vegetales: str):
        super().__init__(nombre, precio)
        self._Proteina = Proteina
        self._Granos = Granos
        self._Vegetales = Vegetales
    def get_proteina(self): 
        return self._Proteina
    def set_proteina(self, p): 
        self._Proteina = p

class Payment:
    def __init__(self, total, metodo="Efectivo", detalle=None):
        self._total = total
        self._metodo = metodo
        self._detalle = detalle
    def get_total(self): 
        return self._total
    def get_metodo(self): 
        return self._metodo
    def get_detalle(self): 
        return self._detalle
    def pagar(self):
        if self._metodo == "Tarjeta":
            print("Pago de ", self._total, "procesado con Tarjeta", self._detalle,)
        else:
            print("Pago de ", self._total, "realizado en ", self._metodo)

class Orden:
    def __init__(self):
        self.items = []

    def AgregarOrden(self, item):
        self.items.append(item)
    
    def Calcular_Plata(self):
        total = 0
        hay_plato_fuerte = any(isinstance(i, PlatoPrincipal) for i in self.items)
        
        for item in self.items:
            precio_actual = item.get_precio()
            if hay_plato_fuerte and isinstance(item, Bebida):
                precio_actual *= 0.80
            
            total += precio_actual
    
        if total > 50000:
            total *= 0.90
    
        return total 

    def resivo(self, metodo=str, detalle=str):
        print("    Recibo Restaurante    ")
        for item in self.items:
            print(item.get_nombre(), "=" ,item.get_precio())
        total_final = self.Calcular_Plata()
        print("Total:", total_final)
        pago = Payment(total_final, metodo, detalle)
        pago.pagar()


#Bebidas
Coca_cola_s = Bebida("Coca-cola", 2500, "Coca-Cola", "350ml")
Coca_cola_m = Bebida("Coca-cola", 4500, "Coca-Cola", "750ml")
Coca_cola_l = Bebida("Coca-cola", 8000, "Coca-Cola", "1500ml")
Pepsi_s = Bebida("Pepsi", 2000, "Postobon", "350ml")
Pepsi_m = Bebida("Pepsi", 4000, "Postobon", "750ml")  
Pepsi_l = Bebida("Pepsi", 6000, "Postobon", "1500ml")
Colombiana_m = Bebida("Colombiana", 4000, "Postobon", "750ml")
Condor_l = Bebida("Kola condor", 5000, "Gaseosas Condor", "2l")

#Aperitivos
chicha = Aperitivo("Chicharron", 6000, 6)
platanito = Aperitivo("Platanos asados", 5500, 4)
cebolla = Aperitivo("Aros de cebolla", 10000, 8)

#Plato fuerte
Pollo_arroz = PlatoPrincipal("Pollo ahumado con arroz", 16000, "Pollo",
                         "Arroz", "Brocoli")
Pollo_Lente = PlatoPrincipal("Pollo con lentejas", 19000, "Pollo",
                         "Lentejas", "Pepino con tomate")
Pollo_frijo = PlatoPrincipal("Pollo con especialidad de la casa", 25000, "Pollo",
                         "Frijoles con carne", "Coliflor ahumado")
Carne_arroz = PlatoPrincipal("Carne asada con arroz", 16000, "Carne",
                         "Arroz", "Brocoli")
Carne_Lente = PlatoPrincipal("Carnecon lentejas", 19000, "Carne",
                         "Lentejas", "Pepino con tomate")
Carne_frijo = PlatoPrincipal("Carne con especialidad de la casa", 25000, "Carne",
                         "Frijoles picantes", "Coliflor ahumado")
conejo = PlatoPrincipal("Especialidad de la casa", 40000, "Conejo",
                    "Frijoles picantes con carne", "Coliflor y brocoli")

mi_orden = Orden()
mi_orden.AgregarOrden(Carne_arroz)
mi_orden.AgregarOrden(Condor_l)
mi_orden.AgregarOrden(Coca_cola_l)
mi_orden.AgregarOrden(chicha)
mi_orden.resivo("Tarjeta", "Visa-4532")

```

   per().compute_inner_angles()
