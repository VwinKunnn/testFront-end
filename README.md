<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Task Manager App</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
</head>
<body>
  <div id="root" class="min-h-screen bg-gray-100"></div>

  <script type="text/babel">
    const { useState, useEffect } = React;

    function TaskManager() {
      const [tasks, setTasks] = useState([]);
      const [title, setTitle] = useState('');
      const [description, setDescription] = useState('');
      const [filter, setFilter] = useState('all');
      
      // Load tasks from localStorage on initial render
      useEffect(() => {
        const savedTasks = localStorage.getItem('tasks');
        if (savedTasks) {
          setTasks(JSON.parse(savedTasks));
        }
      }, []);

      // Save tasks to localStorage whenever they change
      useEffect(() => {
        localStorage.setItem('tasks', JSON.stringify(tasks));
      }, [tasks]);

      const addTask = (e) => {
        e.preventDefault();
        if (!title.trim()) return;
        
        const newTask = {
          id: Date.now(),
          title,
          description,
          completed: false
        };
        
        setTasks([...tasks, newTask]);
        setTitle('');
        setDescription('');
      };

      const toggleTask = (id) => {
        setTasks(tasks.map(task => 
          task.id === id ? { ...task, completed: !task.completed } : task
        ));
      };

      const filteredTasks = tasks.filter(task => {
        if (filter === 'completed') return task.completed;
        if (filter === 'active') return !task.completed;
        return true;
      });

      return (
        <div className="container mx-auto p-4 max-w-2xl">
          <h1 className="text-3xl font-bold text-center mb-8 text-indigo-600">Task Manager</h1>
          
          {/* Add Task Form */}
          <form onSubmit={addTask} className="bg-white p-6 rounded-lg shadow-md mb-6">
            <h2 className="text-xl font-semibold mb-4">Add New Task</h2>
            <div className="mb-4">
              <label className="block text-gray-700 mb-2">Title</label>
              <input
                type="text"
                value={title}
                onChange={(e) => setTitle(e.target.value)}
                className="w-full p-2 border rounded"
                placeholder="Task title"
                required
              />
            </div>
            <div className="mb-4">
              <label className="block text-gray-700 mb-2">Description</label>
              <textarea
                value={description}
                onChange={(e) => setDescription(e.target.value)}
                className="w-full p-2 border rounded"
                placeholder="Task description"
                rows="3"
              />
            </div>
            <button 
              type="submit" 
              className="bg-indigo-600 text-white py-2 px-4 rounded hover:bg-indigo-700 transition"
            >
              Add Task
            </button>
          </form>
          
          {/* Task Filters */}
          <div className="flex space-x-4 mb-6">
            <button 
              onClick={() => setFilter('all')}
              className={`px-4 py-2 rounded ${filter === 'all' ? 'bg-indigo-600 text-white' : 'bg-gray-200'}`}
            >
              All
            </button>
            <button 
              onClick={() => setFilter('active')}
              className={`px-4 py-2 rounded ${filter === 'active' ? 'bg-indigo-600 text-white' : 'bg-gray-200'}`}
            >
              Active
            </button>
            <button 
              onClick={() => setFilter('completed')}
              className={`px-4 py-2 rounded ${filter === 'completed' ? 'bg-indigo-600 text-white' : 'bg-gray-200'}`}
            >
              Completed
            </button>
          </div>
          
          {/* Task List */}
          <div className="bg-white p-6 rounded-lg shadow-md">
            <h2 className="text-xl font-semibold mb-4">
              {filter === 'all' ? 'All Tasks' : 
               filter === 'active' ? 'Active Tasks' : 'Completed Tasks'}
              <span className="ml-2 text-sm text-gray-500">
                ({filteredTasks.length} items)
              </span>
            </h2>
            
            {filteredTasks.length === 0 ? (
              <p className="text-gray-500 text-center py-4">No tasks found</p>
            ) : (
              <ul className="space-y-3">
                {filteredTasks.map(task => (
                  <li 
                    key={task.id} 
                    className={`p-4 border rounded-lg ${task.completed ? 'bg-gray-50' : 'bg-white'}`}
                  >
                    <div className="flex items-start">
                      <input
                        type="checkbox"
                        checked={task.completed}
                        onChange={() => toggleTask(task.id)}
                        className="mt-1 mr-3"
                      />
                      <div className="flex-1">
                        <h3 className={`font-medium ${task.completed ? 'line-through text-gray-500' : 'text-gray-800'}`}>
                          {task.title}
                        </h3>
                        {task.description && (
                          <p className={`text-sm mt-1 ${task.completed ? 'text-gray-400' : 'text-gray-600'}`}>
                            {task.description}
                          </p>
                        )}
                      </div>
                    </div>
                  </li>
                ))}
              </ul>
            )}
          </div>
        </div>
      );
    }

    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<TaskManager />);
  </script>
</body>
</html>
