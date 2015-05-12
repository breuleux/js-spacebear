
SPACE BEAR
==========

A framework for web applications inspired from React and Om, optimized
for the Earl Grey language.

The project is in its infancy and is not yet fully working (but a
decent chunk of the core is done).

In a nutshell, a Spacebear application will function like this:

1. Generate initial application state
2. Generate UI through *render* functions that track what they read
3. Modify the app state through *transact* functions that track what they write
   in order to create a "patch"
4. Spacebear automatically decides what to re-render by comparing
   reads to writes

In other words, Spacebear is a framework which, given a state `s`, a
rendering function `f`, and a patch `p` to the state `s`, knows how to
derive a patch on `f(s)`. In doing so, it minimizes the amount of work
done, whether that be DOM manipulation or computation itself.

Needless to say, rendering functions must be *pure*, devoid of side
effects and must never access any object that may change outside of
Spacebear's strict supervision.


Example
-------

This is how I envision a simple todo application will look like (this
is written in the Earl Grey language):

    require-macros:
       spacebear -> (reactive, transact)
    
    require:
       spacebear -> (System, %)
    
    reactive render-todo(todo) =
       li % todo.description

    reactive render-form(entries, todo) =
       form %
          input %
             value = todo.description
             on-change(e) =
                transact todo: todo.description = e.target.value
          button % 'Add #{entries.length}'
          on-submit() =
             transact {entries, todo}:
                entries.push with {done = false, description = todo.description}
                todo.description = ""
    
    reactive render-todos(todos) =
       div %
          ul % todos.entries each todo -> render-todo(todo)
          render-form(todos.entries, todos.current)

    init = {entries = {}, current = {done = false, description = ""}}
    System(init, render-todos).render-to(document.get-element-by-id("target"))




