## Personal Projects

- [Biblical Repository](https://github.com/pedroaston/rust-bible)

## References

- [Rust Official Tutorial](https://doc.rust-lang.org/stable/book/)

## Journey Tasks

- Continue reading and testing the rust official tutorial.
	- Section 3.2 (https://doc.rust-lang.org/book/ch03-02-data-types.html#the-character-type)


## Raw Notes


- **By default cargo creates git files. How to avoid that**

I had to edit rust's cargo config file for it to not create git files once I run the "cargo new" command. Below we have the modified ~/cargo/config.toml file. If I want i  the future to generate git files with cargo, I need to use the following command "cargo new --vcs git"

```sh
[cargo-new]
vcs = "none"
```


