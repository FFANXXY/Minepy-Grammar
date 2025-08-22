## Latest Syntax Rules  
  ***From ALPHA-half-0.0.3 Internal Beta***  

  1. Storage & Parsing  
       **Scripts are files that store and run scripts.** The loader will read all files with the `.mpy` extension in the `config/minepyloader/scripts` directory. If the directory does not exist, please *run the game once* first, or create the directory directly.

       ***Note on Syntax Rules: The syntax is similar to `Python` and will strictly read content and indentation line by line.***

  2. Header Declaration  
       a. Package Declaration  
         All header declarations start with `#`. Currently, the `package` keyword is supported for parsing. **`package` is used to declare the directory where methods are stored.** Like other languages, the package structure is composed of a combination in the format `[string].[string]...`. During parsing, **methods will be marked under the specified package**. The following are several possible forms of package declaration:
     ``` Java
     #package test
     ```

     ``` Java
     #package test.file
     ```
        It is worth noting that during reading, the loader will read the string as a list. Therefore, if multiple items are entered, the loader will only read the first keyword and the second item's content. Any remaining content separated by spaces will be ignored.  
        There is no fixed requirement for the number of spaces; the text is split using the regular expression `\\s+`. Theoretically, special characters are not allowed. In the current version, there is no check for this yet. ***Please follow the syntax rules and avoid special characters.***

   3. Methods  
        **The method format can refer to `Java`/`C#`.** The naming below will follow `Java` conventions.  
        > [!CAUTION]  
        > **Methods should not be indented; otherwise, an error will occur.**   

         Basic Syntax:  
      `[access modifier] [modifiers...] [return type] [method name][(data typeA nameA, ...)]:`  
          - 1. Access Modifiers  
            - public: Accessible anywhere.  
            - private: Accessible only within the same package.  
            - protect: Protected <sub> Not yet implemented </sub>  
            - default: Default. If not specified, this access level is used. Of course, directly typing default is also acceptable <sub> Not yet implemented </sub>  
         - 2. Modifiers  
            - load: Runs once after the mod is initialized and all code is loaded.  
            - ingame: Indicates the method can only run in-game.  
         - 3. Return Type  
            - See [Data Types](#data-type)  
         - 4. Method Naming Rules  
            - There are no strict rules for method naming, but method invocation does have naming conventions. ***Please follow the syntax rules and use uppercase and lowercase letters for method names.***  
         - 5. Parameters  
           - Parameters must be named strictly in the format `data type name`. Each parameter should be separated by a **comma [,]**. Spaces can be placed before and after the comma. The space between the data type and the name is not strictly defined.  
         - 6. Blank Line Restrictions  
           - Blank lines inside a method are not considered the end of the method. Only when a new definition begins is it considered the end of the method.  
         - 7. Example  
           - Initialization method: `public load void load():` **Note: In the current version, the initialization method cannot take any parameters; otherwise, it will not execute.**

4. Statements  

   ***All statements should be indented***; otherwise, they will be considered methods!

   1. Variables  
      1. Data Types[^1] <a name="data-type"></a>  

      | Keyword | Type |  
      |-----:|-----------|  
      |     `Object` | Object, any data type matches it|  
      |     `String` | String                |  
      |     `void`   | Void<sub>Used in method return types</sub> |  
      |     `null`   | Null<sub>Used in variables, no clear syntax defined yet</sub>|  
      |     `char`   | Character<sub>Not yet implemented</sub>|  
      |     `int`    | Integer               |  
      |     `float`  | Single-precision floating-point number |  
      |     `double` | Double-precision floating-point number |  
      |     `boolean`| Boolean value         |  
      |     `World`  | World                 |  
      |     `Player` | Player                |  

      [^1]: Only currently supported data types are shown.

      2. ***Variable Initialization***  
         1. Literals  
         For literals, you can directly input the corresponding value. For example:

         | Data Type | Literal Notation | Explanation |  
         | ---- | ---- | ---- |  
         | `String` | `"This is a \"text\" "` | **A character set enclosed in double quotes**. Allows using `\` for **escaping**. |  
         | `int` | `123` / `-123` | An integer, supports positive and negative forms[^2] |  
         | `float`| `0.123` `1f` | Decimals are automatically detected and converted from `double` to `float`[^3], or directly converted to `float` using `f`/`F`, or ***hexadecimal*** numbers |  
         | `double` | `0.1231413242` `2d` | Converts literal decimals to double-precision floating-point form |  
         | `boolean` | `true` / `false` | **Preferably** lowercase, represents true/false |  
         | `null` | `null` | Just null, represents empty, will not cause null pointer errors unless the syntax specifies *non-null* |  

         [^2]: Uses `Integer.parseInt()` in Java for detection and conversion. Similarly, `double` uses `Double.parseDouble()`.  
         [^3]: All literal decimals are first converted to double form, then converted to float based on context. This mechanism may be reset after updating **object-oriented** features.

         2. Method Initialization  
         Initialization from methods allows appending a [method call](#method-call) afterward.  
         The common method initialization format is `Method.new()`. Currently supported `.new()` calls include:

            1. String  
               ``` Java
               String a = String.new(<String>)
               ```
               Creates a string from a literal string, equivalent to `String a = "abc"`.  
               Currently **only used for debugging** and will be updated in future versions.

            2. World <sub>Get a dimension world from the server</sub>  
               ``` Java
               World world = World.new(<String>)
               ```
               Here, the String represents the world name.

               | String | World |  
               | ---- | ---- |  
               | `"overworld"` | Overworld |  
               | `"nether"` | Nether |  
               | `"end"` | The End |  

               > [!WARNING]  
               > If no world is running, an error will occur.

            3. Player <sub>Not fully completed yet, expected to be completed in *ALPHA-0.0.3* beta </sub>  
               > [!WARNING]  
               > Subject to change, do not use; content not yet complete, do not use!  
               ``` Java
               Player player = Player.new(<World>)
               ```
               Gets the first player from the provided world. If none exists, it is invalid.

         3. **No Initialization**  
            Only declaring a variable without assigning a value. In the current version, there is no variable assignment operation yet, so this is temporarily useless.

      3. Examples  
         The following will use some code to explain variable declaration and definition, as well as some detailed rules.

         ``` Java
         String a
         ```
         ^ Variable definition without initialization.

         ``` Java
         String a = "123"
         ```
         ^ Literal initialization. This code consists of two nodes: the definition part is composed of `data type name`, and the assignment part is composed of `= assignment`.

         > [!WARNING]  
         > Do not append `;` at the end of variable definitions; it is non-standard and may cause errors.

         ```
         World world = World.new()
         ```
         ^ Method assignment. The assignment part will **take the method's return value as the variable's value**.

   2. Method Call <a name="method-call"> </a>  
      1. Internal Method Calls  
         The internal method package is `mpy.`, so when calling internal methods, you can choose to prefix with `.mpy` or not; it won't affect the result.  
         The following list includes some common methods; initialization `.new()` methods will not be listed here.

         | Internal Class | Internal Method | Effect |  
         | ---- | ---- | ---- |  
         | `Logger` | `log(Object,Object)` | Uses the first **object's** *string* form as the ***name*** and the second as the ***content***, outputs a `log` to the logger. |  
         | `Logger` | `warn(Object,Object)` | Uses the first **object's** *string* form as the ***name*** and the second as the ***content***, outputs a `warning` to the logger. |  
         | `Logger` | `err/error(Object,Object)` | Uses the first **object's** *string* form as the ***name*** and the second as the ***content***, outputs an `error` to the logger. |  
         | `Player` | `kill(Player)` | Attempts to kill a **player**. |  

      2. Package Method Calls  
         Methods are stored using the `package.name` structure. When you want to call a method, follow this structure.  
         For example:
         ``` Java
         #package test

         public void init(String str):
            Logger.log("Test Script", str)
         ```

         ``` Java
         #package main

         public load void main():
            test.init("Script initialized")
         ```
         Output: `(Test Script)Script initialized`

         ---

         For multiple methods with the same name, an **overriding**-like mechanism is used.

         ``` Java
         #package test

         public void init(String str):
            Logger.log("Test Script", str)
            // Override test
         public void init(String str, String s):
            Logger.log(str, s)
         ```

         ``` Java
         #package main

         public load void main():
            test.init("Script initialized")
            test.init("Main Script", "Script initialized")
         ```

         Output:  
         - `(Test Script)Script initialized`  
         - `(Main Script)Script initialized`

5. Comments  
   Comment detection is very simple. Currently, there is only one type: on a blank line with any indentation, input `//`.  
   For example:
   ``` Java
   // This is a comment
   public load void main():
      // It can be anywhere
        // Regardless of indentation
5. Basic Examples

    Kill a player in the overworld

    ``` Java
    #package main

    // Define method
    public ingame void killplayer():
    // Get world
    World world = World.new("overworld")
    // Get player
    Player player = Player.new(world)
    // Kill player
    Player.kill(player)
    ```

6. Command Invocation  
    Methods can also be called via commands.  

    Syntax: `/methods <method>`  

    For example, calling the method from Basic Example 1:  
    `/methods main.killplayer`  