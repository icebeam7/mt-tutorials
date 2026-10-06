# Navigation tutorial

This mini tutorial focuses on **navigation** and **passing arguments** between screens.

## App idea

Let's build a `Study Planner` app.

The app includes:
- a home screen with a list of tasks
- the user taps a task from a list and opens a detail screen,
- the detail screen contains information of the selected task (id, title, completion status, priority),
- a button allows you to mark a task as completed,
- navigation exists between screens with argument passing,

## Learning goal

By the end of the session, students will be able to:
- create multiple screens in Jetpack Compose,
- navigate between screens with `NavController`,
- pass arguments between screens,
- handle different data types (`Int`, `String`, `Boolean`),
- build a small app that is actually useful.

## Structure

1. [.md](Project setup)
2. [.md](Create the home screen)
3. [.md](Create the detail screen)
4. [.md](Pass arguments from one screen to another)
5. [.md](Demo with different types)
6. [.md](Finish with a useful feature)

## Main route pattern

```text
home -> taskDetail/{taskId}/{title}/{isDone}/{priority}
```

## Key concepts

- `NavController`
- `NavHost`
- `composable(...)`
- `navArgument(...)`
- `NavType.IntType`
- `NavType.StringType`
- `NavType.BoolType`

---

[Start the tutorial](01-project-setup.md)
