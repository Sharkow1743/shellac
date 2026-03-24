# Shellac Webview

A Selenium wrapper for building desktop applications using web technologies. It uses FastAPI for backend communication and Selenium for browser automation.

## Installation

```bash
pip install shellac-webview
```

## Basic Usage

```python
from shellac import Window

win = Window()

@win.bind
def my_function(event):
    return "Data from Python"

html = """
<button onclick="run()">Run</button>
<script>
    async function run() {
        const result = await webui.call("my_function");
        console.log(result);
    }
</script>
"""

win.show(html)
win.wait()
```

## Binding

You can bind functions in python to be called from js using `webui.call("func_name")`

### Function Binding
You can bind a specific function to a name.
```python
def my_function(event):
    return f"Hello, {event.data[0]}!"

win.bind("greet", my_function)
```
**JS Usage:**
```javascript
const response = await webui.call("greet", "Alice");
console.log(response); // "Hello, Alice!"
```

### Decorator Binding
Use the `@bind` decorator for a cleaner syntax.
```python
@win.bind
def calculate(event):
    return event.data[0] * 2
```
**JS Usage:** `webui.call("calculate", 21);`

### Class/Namespace Binding
You can bind an entire class or instance. All public methods (not starting with `_`) will be available in JavaScript.
```python
@win.bind("db")
class Database:
    def get_user(self, event):
        return {"id": event.data[0], "name": "John Doe"}
```
or with
```python
win.bind("db", Database())
```
**JS Usage:**
```javascript
const user = await webui.call("db.get_user", 1);
```

### Class Mapping
If you bind a class without a prefix, its methods are mapped to the top-level.
```python
class API:
    def status(self, event):
        return "Online"

win.bind(API)
```
**JS Usage:** `webui.call("status");`

### The `Event` Object
Every bound Python function receives an `Event` object as its first argument.
- `event.window`: The `Window` instance.
- `event.data`: A list of arguments passed from JavaScript.
- `event.element`: The name of the function called.

## Window Configuration

You can configure the window size and behavior before calling `show()`.

```python
win = Window()
win.config.width = 1200
win.config.height = 900
win.config.hide_controls = True # Hides address bar/tabs (App Mode)
win.config.kiosk = False        # Fullscreen mode
```

## Browser Selection

By default, the library looks for Chrome, Edge, or Firefox. You can force a specific browser:

```python
from webui import Browser

win.show("index.html", browser=Browser.Firefox)
```

Available options: `Chrome`, `Firefox`, `Edge`, `Chromium`, `Brave`, `Vivaldi`.

## Other functions

Once the window is running, you can navigate to new pages or execute JavaScript from Python.

```python
# Navigate to a new URL
win.navigate("https://google.com")

# Execute JS directly
win.run_js("alert('Hello from Python!')")

# Change title
win.set_title("New Title")
```