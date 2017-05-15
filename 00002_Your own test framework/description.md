And what if there is nothing like a test runner in your technology? Then you have to implement it yourself :unamused:. Buuuut, there are some good news! `mumukit` provides some components to do it quicker: `Mumukit::Metatest::Framework` :sunglasses:.

There are two different ways to accomplish this: 

  * If you don't need Docker support - becasue you can do all the test securely and in-memory - you should drop and forget about `Mumukit::Templates::FileHook`, and implement your `test_hook` using `Mumukit::Hook` as parent class. 
  * If you will need Docker support to run the code, you should still inherit from `Mumukit::Templates::FileHook`, as usual, and executing your assertions within `post_process_file`.

Since the latter scenario is the most common, we already provide a specific directive `metatested true`:

```ruby
class MyRunnerTestHook < Mumukit::Templates::FileHook
  isolated true
  metatested true

  def tempfile_extension
    '...'
  end

  def command_line(filename)
      "customcommand #{filename}"
  end

  def metatest_checker
    ...return a Mumukit::Metatest::Checker that implements your assertions....
  end
  
  def compile_metatest_file_content(request)
     ...return the input to your custom command....
  end
end
```

The only restriction is that your `customcommand` must return a Json output. If it is not the case, please override `to_metatest_compilation(result)` to convert its output to a proper hash with symbolized keys. 

Please notice that using `metatested true` is incompatible with `structured true` and `mashup` directives. 