# now-rust

> Community based builder for using rust on the now/zeit platform

This is a now builder which allows you to run your rust code as lambdas on the now platform!

This was originally provided officially by [ZEIT](https://zeit.co)'s [now-builders](https://github.com/zeit/now-builders) monorepo, but has since been moved to a community-maintained project.

- [Usage](#usage)
  - [Entrypoint](#entrypoint)
  - [Dependencies](#dependencies)
  - [Example](#example)
- [FAQ](#faq)
- [Contributing](#contributing)
- [License](#license)
- [Contributors](#contributors-)

## Usage

If you're unfamiliar with now runtimes, please read the [runtime docs](https://zeit.co/docs/runtimes) first. This runtime can be used like any other Community Runtime.

```json
{
	"functions": {
		"api/**/*.rs": {
			"runtime": "now-rust@1.0.0"
		}
	}
}
```

That's the simplest way to use this runtime!

### Entrypoint

The entrypoint, in this case every file that matches `api/**/*.rs`, is used to create a Serverless Function for you. Note that the `Cargo.toml` file must exist on the same level as the `.rs` files.

The requirements for this entrypoint is to expose a `handler` function and not to have a `main` function.

### Dependencies

This Builder supports installing dependencies defined in the `Cargo.toml` file.

Furthermore, more system dependencies can be installed at build time with the presence of a shell `build.sh` file in the same directory as the entrypoint file.

#### Example

This could be our `api/user.rs` file:

```rust
use http::{StatusCode};
use now_lambda::{error::NowError, IntoResponse, Request, Response};

fn handler(_: Request) -> Result<impl IntoResponse, NowError> {
	let response = Response::builder()
		.status(StatusCode::OK)
		.header("Content-Type", "text/plain")
		.body("user endpoint")
		.expect("Internal Server Error");

		Ok(response)
}
```

Our `api/Cargo.toml` could look like this:

```toml
[package]
name = "index"
version = "1.0.0"
authors = ["Mike Engel <mike@mike-engel.com>"]
edition = "2018"

[dependencies]
http = "0.1"
now_lambda = "0.1"
```

Finally we need a `now.json` file to specify the runtime for `api/user.rs`:

```json
{
	"functions": {
		"api/**/*.rs": {
			"runtime": "now-rust@1.0.0"
		}
	}
}
```

## FAQ

### Are cargo workspaces supported?

Not quite. Cargo's workspaces feature is a great tool when working on multiple binaries and libraries in a single project. If a cargo workspace is found in the entrypoint, however, now-rust will fail to build.

To get around this limitation, create build entries in your now.json file for each Cargo.toml that represents a lambda function within your workspace. In your .nowignore, you'll want to add any binary or library project folders that aren't needed for your lambdas to speed up the build process like your Cargo.toml workspace.

It's also recommended to have a Cargo.lock alongside your lambda Cargo.toml files to speed up the build process. You can do this by running cargo check or a similar command within each project folder that contains a lambda.

If you have a compelling case for workspaces to be supported by now-rust which are too cumbersome with this workaround, please submit an issue! We're always looking for feedback.

### How do I use this during local development?

The `now dev` command allows you to develop lambdas locally on your machine. With `now dev` and `now-rust` you can develop your rust-based lamdas on your own machine.

During local development with `now dev`, the assumption is that `rust` and `cargo` are already installed and available in your `PATH` since they will not be installed automatically. The recommended way to install `rust` and `cargo` on your machine is with [rustup](https://rustup.rs).

### Can I use musl/static linking?

Unfortunately, the AWS lambda runtime for rust relies (tangentially) on `proc_macro`, which won't compile on musl targets. Without `musl`, all linking must be dynamic. If you have a crate that relies on system libraries like `postgres` or `mysql`, you can include those library files with the `includeFiles` config option and set the proper environment variables, config, etc. that you need to get the library to compile.

For more info, please see [issue #2](https://github.com/mike-engel/now-rust/issues/2).

### Why does this project use tabs over spaces?

Please refer to [this tweet](https://twitter.com/devdevcharlie/status/1146571021564043264).

## Contibuting

Please note that this project is released with a [Contributor Code of Conduct](CODE_OF_CONDUCT.md). By participating in this project you agree to abide by its terms.

Issues and pull requests are welcome!

### Setup

Since this project contains both rust and node code, you need to install the relevant dependencies. If you're only working on the javascript side, you only need to install those dependencies. The oppoosite is true for the rust side.

```sh
# install node dependencies
npm install

# install cargo dependencies
cargo fetch
```

At this point, you're all set up and can start making edits!

**Note: During the migration period, tests will be broken until we get CI set up!**

## License

[MIT](LICENSE.md)

## Contributors ✨

Thanks goes to these wonderful people ([emoji key](https://allcontributors.org/docs/en/emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore -->
<table>
  <tr>
    <td align="center"><a href="https://www.mike-engel.com"><img src="https://avatars0.githubusercontent.com/u/464447?v=4" width="100px;" alt="Mike Engel"/><br /><sub><b>Mike Engel</b></sub></a><br /><a href="#question-mike-engel" title="Answering Questions">💬</a> <a href="https://github.com/Mike Engel <mike@mike-engel.com>/now-rust/commits?author=mike-engel" title="Code">💻</a> <a href="https://github.com/Mike Engel <mike@mike-engel.com>/now-rust/commits?author=mike-engel" title="Documentation">📖</a> <a href="#example-mike-engel" title="Examples">💡</a> <a href="https://github.com/Mike Engel <mike@mike-engel.com>/now-rust/commits?author=mike-engel" title="Tests">⚠️</a> <a href="#review-mike-engel" title="Reviewed Pull Requests">👀</a> <a href="#maintenance-mike-engel" title="Maintenance">🚧</a> <a href="#design-mike-engel" title="Design">🎨</a> <a href="#infra-mike-engel" title="Infrastructure (Hosting, Build-Tools, etc)">🚇</a> <a href="#ideas-mike-engel" title="Ideas, Planning, & Feedback">🤔</a> <a href="#content-mike-engel" title="Content">🖋</a></td>
    <td align="center"><a href="https://twitter.com/_anmonteiro"><img src="https://avatars2.githubusercontent.com/u/661909?v=4" width="100px;" alt="Antonio Nuno Monteiro"/><br /><sub><b>Antonio Nuno Monteiro</b></sub></a><br /><a href="#question-anmonteiro" title="Answering Questions">💬</a> <a href="https://github.com/Mike Engel <mike@mike-engel.com>/now-rust/commits?author=anmonteiro" title="Code">💻</a> <a href="https://github.com/Mike Engel <mike@mike-engel.com>/now-rust/commits?author=anmonteiro" title="Documentation">📖</a> <a href="#example-anmonteiro" title="Examples">💡</a> <a href="https://github.com/Mike Engel <mike@mike-engel.com>/now-rust/commits?author=anmonteiro" title="Tests">⚠️</a> <a href="#review-anmonteiro" title="Reviewed Pull Requests">👀</a> <a href="#maintenance-anmonteiro" title="Maintenance">🚧</a> <a href="#design-anmonteiro" title="Design">🎨</a> <a href="#infra-anmonteiro" title="Infrastructure (Hosting, Build-Tools, etc)">🚇</a> <a href="#ideas-anmonteiro" title="Ideas, Planning, & Feedback">🤔</a> <a href="#content-anmonteiro" title="Content">🖋</a></td>
    <td align="center"><a href="https://www.mischka.me"><img src="https://avatars1.githubusercontent.com/u/3939997?v=4" width="100px;" alt="Jacob Mischka"/><br /><sub><b>Jacob Mischka</b></sub></a><br /><a href="https://github.com/Mike Engel <mike@mike-engel.com>/now-rust/commits?author=jacobmischka" title="Code">💻</a></td>
  </tr>
</table>

<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!
