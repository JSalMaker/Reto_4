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
```
ejercicio 2
    
    def compute_inner_angles(self):
        return super().compute_inner_angles()
