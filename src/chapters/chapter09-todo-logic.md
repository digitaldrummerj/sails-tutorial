## Todo API Logic

### Create Todo

<h4 class="exercise-start">
    <b>Exercise</b>: Create Todo
</h4>


```javascript
create: function createFn(req, res) {
    var todo = {
      item: req.param('item'),
      completed: req.param('completed'),
      user: req.session.user
    };

    Todo.create(todo, function (err, results) {
      if (err) res.serverError(err);
      return res.ok(results);
    });
},
```

1. Todo.js

```javascript
item: {
    required: 'true',
    type: 'string'
},
completed: {
    required: 'true',
    type: 'boolean',
    defaultsTo: 'false'
},
```

<div class="exercise-end"></div>

### Get Todo List

<h4 class="exercise-start">
    <b>Exercise</b>: Get Todo List
</h4>

```javascript
find: function findFn(req, res) {
    Todo.find(
        { 
            user: req.session.user 
        }
    )
    .populate('user')
    .exec(function (err, results) {
        if (err) return res.serverError(err);
        if (results.length === 0) return res.notFound();

        return res.ok(results);
    });
},
```

<div class="exercise-end"></div>

### Get Single Todo 

<h4 class="exercise-start">
    <b>Exercise</b>: Find Single Todo Item
</h4>

```javascript
findOne: function findOneFn(req, res) {
    Todo.findOne(
        { 
            user: req.session.user 
        }
    )
    .populate('user')
    .exec(function (err, result) {
        if (err) return res.serverError(err);
        if (!result) return res.notFound();

        return res.ok(result);
    });
}
```

<div class="exercise-end"></div>

### Update Todo

<h4 class="exercise-start">
    <b>Exercise</b>: Update Single Todo
</h4>

```javascript
update: function (req, res) {
    Todo.findOne(
        { 
            user: req.session.user, 
            id: req.param('id') 
        }, 
        function (err, found) {
            found.item = req.param('item');
            found.completed = req.param('completed');

            Todo.update(
                {
                    user: req.session.user,
                    id: req.param('id')
                }, 
                found,
                function (err, results) {
                    if (err) res.serverError(err);
                    return res.ok(results);
                }
            );
        }
    );
},
```


<div class="exercise-end"></div>

### Delete Todo

<h4 class="exercise-start">
    <b>Exercise</b>: Delete Single Todo
</h4>

```javascript
delete: function (req, res) {
    Todo.delete(
        { 
            user: req.session.user, 
            id: req.param('id'),
        }, 
        function (err, results) {
            if (err) res.serverError(err);
            return res.ok(results);
        }
    );
},
```

<div class="exercise-end"></div>

### Find Only Not Completed Todo

<h4 class="exercise-start">
    <b>Exercise</b>: Create Method to Return Only Open
</h4>

```javascript
findOnlyOpen: function findOnlyOpenFn(req, res) {
    Todo.find({
        user: req.session.user,
        completed: false,
    })
    .populate('user')
    .exec(function (err, results) {
        if (err) return res.serverError(err);
        if (results.length === 0) return res.notFound();

        return res.ok(results);
    });
},
```


<div class="exercise-end"></div>

### Associating User to Todo Items

<h4 class="exercise-start">
    <b>Exercise</b>: Associate Models
</h4>

1. Todo.js

    ```javascript
    user: {
      model: 'user'
    }
    ```

1. User.js

    ```javascript
    todoItems: {
      collection: 'todo',
      via: 'user'
    },
    ```

<div class="exercise-end"></div>
