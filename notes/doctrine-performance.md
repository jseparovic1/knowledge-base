[Ocramius PHP UK Doctrine ORM](https://www.youtube.com/watch?v=rzGeNYC3oz0)](https://www.notion.so/Ocramius-PHP-UK-Doctrine-ORM-e8af69f00bc644ca8b5e9df95a61fdbd)

Talk source [Ocramius PHP UK Doctrine ORM](https://www.youtube.com/watch?v=rzGeNYC3oz0)](https://www.notion.so/Ocramius-PHP-UK-Doctrine-ORM-e8af69f00bc644ca8b5e9df95a61fdbd)

- Use SQL for reporting
- Entities should work without db
- Don't expose Collection (return arrays instead)
- Entities must always be in valid state
    - Use UUID not auto increment id's
    - Use named constructors to create from or other inputs
    - Add behaviour to entities, don't relay on getters/setters only
- Lifecycle callbacks are mostly hack us it with caution
- Avoid derived and composite primary keys
- Favor immutable data (favor append only collections)

    ```php
    class PrivateMessage
    {
        private $from;
        private $to;
        private $message;
        private $read = [];

        public function __construct(
            User $from,
            User $to,
            string $message
        ) {
            // ...
        }

        public function read(User $user) {
            $this->read[] = new MessageRead($user, $this);
        }
    }
    ```

- Avoid soft deletes
    - They break data integrity (some data may reference soft deleted data)
    - They break immutability
- Eager loading is useless (Marko say's). Eager loading basically just does join automatically if it can.
- Be carefully with Extra Lazy
- Avoid bi-directional associations if possible (adds additional overhead because it hydrates on both ends)
- Consider Query functions

    ```php
    final class UsersThatHaveAMonthlySubscription
    {
        public function __construct(EntityManagerInterface $em) {
            // ...
        }

        public function __invoke() : Traversable
        {
            // ... INSERT DQL/SQL HELL HERE ...
        }
    }
    ```

- Doctrine don't repeats until UoW is not cleared since it keeps everything in memory
- Separate get and find methods on repository (get thrrows exception , find returns null)
- Try to avoid pagination (better to use a custom query). Paginator is designed to support all different databases
  
Useful links:
- [Tideways 5 ORM performance traps to avoid](https://tideways.com/profiler/blog/5-doctrine-orm-performance-traps-you-should-avoid)
- [Ocramius ORM Hydration Performance optimization](https://ocramius.github.io/blog/doctrine-orm-optimization-hydration/)
- [Official doctrine performance docs](https://www.doctrine-project.org/projects/doctrine-orm/en/2.7/reference/improving-performance.html)
- [Effective Doctrine](https://pt.slideshare.net/marcinchwedziak/effective-doctrine2-performance-tips-for-symfony2-developers-33907944)
