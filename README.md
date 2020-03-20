# evote-movie-2020-12-controller-superclass

We have some duplication in our code, since both controller classes have to do some of the same things:

- they define a constant for the `PATH_TO_TEMPLATES`

- they declare a private instance variable `$twig`

- they create a Twig object in the constructor method `__construct()`

Let us refactor our code to abstract out the common actions into a `Controller` superclass, from which all of controller classes will `extend`

- create the `Controller` superclass:

    ```php
    <?php
    namespace Tudublin;
    
    class Controller
    {
        const PATH_TO_TEMPLATES = __DIR__ . '/../templates';
        protected $twig;
    
        public function __construct()
        {
            $this->twig = new \Twig\Environment(new \Twig\Loader\FilesystemLoader(self::PATH_TO_TEMPLATES));
        }
    }
    ```
  
    - NOTE: We have made the instance variable `$twig`
**protected** rather than **private**, since we **do** want subclasses to be able to directly access this variable

- simplify `MainController` by declaring it as a sub-class of `Controller`, and removing the instance variable `$twig` and the constructor method:

    ```php
    <?php
    namespace Tudublin;
    
    class MainController extends Controller
    {
        public function home()
        {
            $template = 'index.html.twig';
            $args = [];
            $html = $this->twig->render($template, $args);
            print $html;
        }
    
        ... and so on ..
    ```
- refactoring the `MovieController` class needs a little bit more work, since as well as creating the `$twig` instance variable, it also created an instance variable `$movieRepository`. So we have to still have a constructor method, but we need to make it first invoke its parent (superclass) constructor (to create `$twig`) before doing other initisation actions:

    ```php
    <?php
    namespace Tudublin;
    
    use Mattsmithdev\PdoCrudRepo\DatabaseTableRepository;
    
    class MovieController extends Controller
    {
        private $movieRepository;
    
        public function __construct()
        {
            parent::__construct();
            $this->movieRepository = new MovieRepository();
        }
    ```

    - in PHP we use `parent::<METHOD>` to invoke a method in a superclass ...
    