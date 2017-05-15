  Sometimes, the mashup directive is not enough, and you need to pass more complex code to your command. For those cases, **instead of** using `mashup`, you should implement `compile_file_content(request)`. E.g: 

  ```ruby
  class JavaTestHook < Mumukit::Templates::FileHook
    isolated true
    # notice no mashup directive!

    def compile_file_content(request)
      <<EOF
  import java.util.*;
  import java.util.function.*;
  import java.util.stream.*;
  import java.time.*;
  import org.junit.*;

  #{request.content}

  #{request.extra}

  public class SubmissionTest {
  #{request.test}

  public static void main(String args[]) {
    org.junit.runner.JUnitCore.main("SubmissionTest");
  }
  }
  EOF
    end
  end
  ``` 

  The `compile_file_content(request)` must return a string. The `Mumukit::Templates::TestHook` will then compile it into a temporal file and pass it to your command. 