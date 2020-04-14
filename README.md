# SOLID 

Como sabemos la Programacion Orientada a Objetos nos ayuda a diseñar software de una forma sencilla, ¿pero que pasa cuando aun usando este enfoque de programación nuestro código se vuelve complejo y dificil de entender? 

Esto es lo que SOLID resuelve, siendo una serie de principios o guías de diseño que nos ayudan en la labor de crear programas legibles y mantenibles.

Estos principios, hacen que sea fácil para un programador desarrollar software que sea fácil de mantener y ampliar. También facilitan a los desarrolladores refactorizar fácilmente el código y forman parte del desarrollo ágil de software ademas de que favorece una mayor reusabilidad y calidad del código, así como la encapsulación.

## Ventajas

- Mantenimiento del código más fácil y rápido
- Permite añadir nuevas funcionalidades de forma más sencilla
- Favorece una mayor reusabilidad y calidad del código, así como la encapsulación

## S: Single Responsibility Principle

Establece que cada clase debe tener una sola función o tarea a cumplir

- Forma incorrecta

```ts
class Book {
    getTitle(): string {
        return "Titulo"
    }

    getAuthor(): string {
        return "Autor"
    }

    turnPage(): string {
        return "Siguiente página"
    }

    printCurrentPage(): string {
        return "Imprimir página"
    }
}
```
Como vemos una misma clase ademas de tener los getters tambien tiene la funcion de paginado asi como de imprimir.

- Forma correcta

```ts
class Book {
    getTitle(): string {
        return "Titulo"
    }

    getAuthor(): string {
        return "Autor"
    }
}

class Pager {

    gotoPrevPage(): string {
        return "Anterior página"
    }

    gotoNextPage(): string {
        return "Siguiente página"
    }

    gotoPageByPageNumber(pagerNumber: number): string {
        return "Ir a la página"
    }
}

class Printer { 
    printPageInHTML(pagina: any): string { 
        return "Imprimir página"
    }

    printPageInJSON(pagina: any): string { 
        return "Imprimir página"
    }

    printPageInXML(pagina: any): string { 
        return "Imprimir página"
    }
}
```

Se seperan las funciones en diferentes clases.

## O: Open Closed Principle

Establece que las clases o entidades deberían estar abiertos para su extensión, pero cerrados para su modificación.

- Forma incorrecta

```ts
class Pdf {
  constructor(name, size) {
    this.name = name;
    this.size = size;
  }

  // ...
}

class Csv {
  constructor(name) {
    this.name = name;
  }

  // ...
}

class Printer {
  function printFile(file) {
    if (file instanceof Pdf) {
      // Print Pdf...
    } else if (file instanceof Csv) {
      // Print Csv...
    }
  }
}
```
Notamos que la clase _Printer_ sufriria cambios cada vez que se agreguen mas extensiones de archivos, lo que va en contra de este principio.

- Forma correcta

```ts
class Printable {
    print() {
    // ...
    }
}
class Pdf extends Printable {

    constructor(name: string, size: number) {
        super();
        this.name = name;
        this.size = size;
    }

  // Override
    print() {
    // ...
   }
}
class Csv extends Printable {
    constructor(name: string) {
        super();
        this.name = name;
    }

  // Override
  print() {
    // ...
}
}
class Printer {
    printFile(file: Printable) {
        file.print();
    }
}
```
Ahora la clase _Printer_ no necesitaria saber que extension tiene el documento para poder imprimir.

## L: Liskov Substitution Principle

Declara que una subclase debe ser sustituible por su superclase, y si al hacer esto, el programa falla, estaremos violando este principio.

```ts
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  set width(width: number) {
    this.width = width;
  }

  set height(height: number) {
    this.height = height;
  }

  get area(): number {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  set width(width: number) {
    this.width = width;
    this.height = width;
  }

  set height(height: number) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach((rectangle) => {
    rectangle.width(4);
    rectangle.height(5);
    const area = rectangle.getArea();
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```
Como vemos, _Squeare_ sobrescribe la clase _Rectangle_ en los setters por lo que no se cumpliria este princicpio.

- Forma correcta

```ts
class Rectangle {
  constructor(width: number, height: number) {
    this.width = width;
    this.height = height;
  }

  get area(): number {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
    constructor(size: number) {
        super(size, size);
    }
}

renderLargeRectangles(rectangles: []) {
  rectangles.forEach((rectangle) => {
    const area = rectangle.area();
  });
}

const rectangles = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(4)];
renderLargeRectangles(rectangles);
```

## I: Interface Segregation Principle

Este principio define que una clase no debe ser forzada a depender de interfaces(propiedades o métodos) que no usa.

- Ejemplo:
```ts
interface Animal { 
    fly();
    run();
}

class Eagle implements Animal { 
    fly() { }
    run() { }
}

class Dog implements Animal { 
    fly() { }
    run() { }
}
```
Como vemos, Dog hereda el método fly, algo que no es coherente.

- Forma correcta 
```ts
interface FlyingAnimal { 
    fly()
}

interface FlightlessAnimal { 
    run()
}

class Eagle implements FlyingAnimal {
    fly() { }
}

class Ostrich implements FlightlessAnimal {
    run() { }
}
```

## D: Dependency Inversion Principle

El principio DIP dice que si una clase depende de otras clases, esta relación debería estar en las interfaces.

- Forma incorrecta
```ts
class Cement {
  // implementation
}

class Brick {
  // implementation
}

class Home {
  constructor(cement: number, brick: number) {
    this.cement = cement
    this.brick = brick
  }

  cost(): number {
    // calculate the cost
  }
}
```
La clase Building tiene que conocer la clase Cement para poder calcular el coste de la construcción

```ts
class Material {
  // implementation
}

class Brick extends Material {
  // implementation
}

class Cement extends Material {
  // implementation
} 

class Construction {
  constructor(material: Material) {
    this.material = material
  }

  cost(): number {
    // calculate the cost
  }
}

class Home extends Construction {
  cost(): number {
    // calculate the cost
  }
}
```