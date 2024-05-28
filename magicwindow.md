# ❉ magicwindow ❉

`magicwindow` is a "self aware puzzle game" from ["BootMeCrackMe"](https://xlogicx.net/BootMe_-_CrackMe.html). These puzzles are boot loaders, and can be ran with `qemu-system-i386 magicwindow`.

Once booted, `d` shows the hex dump of the program. `Enter` clears the display, and allows you to enter two hex values with `i` and `Enter`.

Let's look at the initial `d` screen:

![magicwindow_d](https://github.com/coldwx/coldwx.github.io/assets/170867841/c41344e0-33e4-4240-8ec0-3245146ce83e)

We see the hex dump of the 512 byte puzzle.

Now we can input two hexadecimal values to try to get a reaction:

1. `Enter` to clear the screen
2. `i` to change to the input mode
3. `FF` and `Enter`
4. `d` to display the new dump

![magicwindow_d2](https://github.com/coldwx/coldwx.github.io/assets/170867841/167d4bc6-89e6-42e6-97f5-fcc3e06b50a4)

Comparing the first screenshot with the second reveals that the `FF` was placed below the 21 and above the 00 bytes.

![magicwindow_d3e](https://github.com/coldwx/coldwx.github.io/assets/170867841/88dd8e87-9bc0-44fa-96ea-a2dd41e6535e)

Counting all the bytes, our `FF` is located in spot 400 of 512.
