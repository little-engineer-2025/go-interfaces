# Strategies to organize interfaces in golang

When we write code and we want to apply the Dependency Inversion Principle (DIP),
interfaces comes to the table.

Interfaces allows us to abstract the behaviour which will be
provided by a specific component, and when we align with DIP
our code will reference this interfaces.

But when we reference the interface, we reference the package
that contain it, importing all the packages, and its dependencies
which could evoke a cyclic dependencies situation.

Below are exposed a few strategies that could help to organize
the code to reduce the risk of cyclic dependencies.

- Create a directory only for the interfaces for the current package.
- Create a base directory for the interfaces, and a deeper level
  for the specific implementations.
- Create a parallel directory layout which only contain interfaces.

## Create a directory only for the interfaces

It is the easier to implement, and useful when we are starting to
decouple the code. For instance:

```raw
/pkg
  /cars
  /bikes
  /houses
```

Where we have all the components implemented and their interfaces
in the same directory. We could extract the interfaces in a
different package kind of the below:

```raw
/pkg
  /cars
    /inter
  /bikes
    /inter
  /houses
    /inter
```

And we move the interfaces to the `/inter` package.

Upsides:
- Quick solution.
- Useful for starting to move interfaces to different package.

Downsides:
- Add an extra level on every package with no semantic rather
  than an interface is defined.

## Create a base directory for the common interface

This is useful when we organize different implementations for the
same given interface. Let's say we have a `Car` interface, which
is implemented by `SUVCar`, `SportCart`, `Vagon`. It could looks
like the below.

```raw
/pkg
  /cars     # Define the interface here
    /suv    # Specific implementation for SUV
    /sport  # Specific implementation for Sport
    /wagon  # Specific implementation for Wagon
```

Upsides:
- The package path provide some semantic `pkg/cars` and `pkg/cars/sport`.

Downsides:
- Could be difficult to extract the common behavior for several components,
  depending on the complexity of the components.

## Create a parallel directory for the interfaces

This is useful when the implementation layout is very diversive,
and we move a parallel directory layout for the interfaces.

```raw
/pkg
  /cars
  /motorcycles
  /houses
  /bikes
```

We can extract the interfaces and implementations in a way similar to:

```raw
/pkg
  /interface
    /cars
    /motorcycles
    /houses
    /bikes
  /impl
    /cars
    /motorcycles
    /houses
    /bikes
```

Example: https://github.com/avisiedo/go-microservice-1?tab=readme-ov-file#repository-layout

Upsides:
- Could be good when we have several packages to extract, and it provides
  a better semantic than deeper package level, and the interface imported
  is referenced with the same name as the package.
- Could be good when auto-generating mocks, for instance, autogenerating them
  to `/internal/mocks` directory, with the same layout as the interfaces.

Downsides:
- Add an extra level of packages for the interfaces and implementation.

## FAQ

- Why not a unique package for all the interfaces?
  - When the number of interfaces grow, it evokes a mess with the interfaces
    growing in the same way the number of files.
  - The interfaces could depends on several data types, which could evoke
    transitive dependencies for being all the interfaces in the same directory
    and at some point evoke the cyclic dependencies.
- Should I choose one strategy for the whole repository?
  - No. The can be combined as needed in the same repository. Choose
    what fit better your scenario.

