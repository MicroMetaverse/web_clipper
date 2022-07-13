# Rust/Rocket: the trait `serde::ser::Serialize` is not implemented for struct - Stack Overflow
I'm trying to make a simple endpoint using Rocket. My `Cargo.toml` has these dependencies:

```null
[dependencies]
rocket = "0.4.2"
rocket_codegen = "0.4.2"
rocket_contrib = "0.4.2"
```

`main.rs` looks like:

```null
#[macro_use]
extern crate rocket;

use rocket_contrib::json::Json;
use serde::Serialize;

#[get("/org")]
fn getorg() -> Json<Org> {
    Json(Org {
        id: Option::from(25),
        name: "Incredible-Customer".to_string(),
    })
}
#[derive(Serialize)]
pub struct Org {
    pub id: Option<i32>,
    pub name: String,
}

fn main() {
    rocket::ignite().mount("/", routes![getorg]).launch();
}
```

Compiling results in the errors:

    error[E0432]: unresolved import `serde`
     --> src/main.rs:3:5
      |
    3 | use serde::Serialize;
      |     ^^^^^ use of undeclared crate or module `serde`

    error: cannot determine resolution for the derive macro `Serialize`
      --> src/main.rs:14:10
       |
    14 | #[derive(Serialize)]
       |          ^^^^^^^^^
       |
       = note: import resolution is stuck, try simplifying macro imports

    error[E0658]: `macro` is experimental
     --> src/main.rs:7:1
      |
    7 | #[get("/org")]
      | ^^^^^^^^^^^^^^
      |
      = note: see issue #39412 <https://github.com/rust-lang/rust/issues/39412> for more information
      = help: add `#![feature(decl_macro)]` to the crate attributes to enable
      = note: this error originates in an attribute macro (in Nightly builds, run with -Z macro-backtrace for more info)

    error[E0277]: the trait bound `Org: serde::ser::Serialize` is not satisfied
       --> src/main.rs:8:16
        |
    8   | fn getorg() -> Json<Org> {
        |                ^^^^^^^^^ the trait `serde::ser::Serialize` is not implemented for `Org`
        |
       ::: /Users/shep/.cargo/registry/src/github.com-1ecc6299db9ec823/rocket-0.4.5/src/handler.rs:202:20
        |
    202 |     pub fn from<T: Responder<'r>>(req: &Request, responder: T) -> Outcome<'r> {
        |                    ------------- required by this bound in `handler::<impl rocket::Outcome<rocket::Response<'r>, rocket::http::Status, rocket::Data>>::from`
        |
        = note: required because of the requirements on the impl of `Responder<'_>` for `rocket_contrib::json::Json<Org> 

I'm very confused as to how to look into this error. Is it a dependency issue? Why? I have versioned the `rocket` dependencies to the same one, but apparently this `serde` dependency is unhappy. Googling around claims it's a version mismatch inside one of my dependencies - but how do I go about solving that myself? 
 [https://stackoverflow.com/questions/63882114/rust-rocket-the-trait-serdeserserialize-is-not-implemented-for-struct](https://stackoverflow.com/questions/63882114/rust-rocket-the-trait-serdeserserialize-is-not-implemented-for-struct)
