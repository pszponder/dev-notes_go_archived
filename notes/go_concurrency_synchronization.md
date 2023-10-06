# Synchronization

## Intro to Synchronization

Managing data across multiple [goroutines](go_concurrency_goroutines.md) can become problematic and hard to debug

- Multiple goroutines can change the same data leading to unpredictable results
- Using [channels](go_concurrency_channels.md) to communicate is not always ideal

`Synchronization` solves this issue, enabling:

- Waiting for goroutines to finish
- Prevents multiple goroutines from modifying data simultaneously

## Synchronization Techniques

- [WaitGroups](go_concurrency_waitgroups.md) -- Blocks code until all goroutines complete execution (when WaitGroup counter goes to 0)
- [Mutexes](go_concurrency_mutexes.md) -- Enables safe access of data across goroutines

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
