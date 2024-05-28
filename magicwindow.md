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

Counting all the bytes, `FF` is located in spot 400 of 512. Because the puzzle is in hexadecimal, we'll convert that decimal of 400 to a hex of 0x190. The program itself starts at a location of 0x7c00. 0x7c00 + 0x190 reveals our `FF` to be located at 0x7d90.

With knowledge of what the puzzle is modifying, we can disassemble the code with `objdump -D -b binary -mi386 -Maddr16,data16 -M intel magicwindow`.

This will produce an output where we can view the assembly code. One line stands out:

![magicwindow_dis](https://github.com/coldwx/coldwx.github.io/assets/170867841/d6c58a8c-7b99-4b4a-a446-1369bc7da88c)

Our location of interest, 0x7d90, is being compared to 0x58. If equal, the program then runs code located at 0x12d. Let's use 0x58 as the input.

![magicwindow_58](https://github.com/coldwx/coldwx.github.io/assets/170867841/654db1dc-2138-4ef6-a0ed-edd8ea60c025)

Nothing happens. `magicwindow` will display a colorful, animated screen when you input the correct values. There has to be more to the logic.

One tool for disassembly that will make the code a little easier to read is [radare2](https://rada.re/n/radare2.html).

`r2 -b 16 magicwindow` and then `v` will show the code in a colorful way along with some helpful looping graphics on the side.

![magicwindow_r21](https://github.com/coldwx/coldwx.github.io/assets/170867841/09867aca-b3ab-470c-b93a-bca17ff253be)

This confirms that we will jump to location 0x12d if our input is 0x58. There appears to be more logic after that though.

![magicwindow_r22](https://github.com/coldwx/coldwx.github.io/assets/170867841/966deaee-8f7a-4c26-b15f-4ef56a3f6580)

Locations 0x7d93 and 0x7d92 seem to be mentioned. If we look at the first two screenshots from the top of the page, we see the values at those locations. One remains the same, yet one has changed.

![magicwindow_r324](https://github.com/coldwx/coldwx.github.io/assets/170867841/01503f65-aa6a-4858-a1ce-e9a2e682963b)

Infact, every so often, 0x7d93 will change while 0x7d92 does not.

Early on in the code, the program appears to be moving a BIOS clock, 0x46c, into those two locations. It is being changed because of time!

![magicwindow_clock](https://github.com/coldwx/coldwx.github.io/assets/170867841/b284534c-30dd-4a69-af98-005bade58e90)

Back to the code following our comparison to 0x58, the program is moving 0x7d93 into the higher portion of the `ax` register, and 0x7d92 into the lower portion. These registers are `ah` and `al`, respectfully. 

| AH | AL |
| --- | --- |
| 0x7d93 | 0x7d92 |
| 5E | 57 |

`al` is then subtracted from `ah`. It is then compared to 0xf, or 15 in decimal. If the difference is above 15, it is then compared to 0x11, or 17 in decimal. Finally, if the difference is below 17, the program continues on. Below 17, yet above 15, should be 16, or 0x10.

While the intial value of 0x7d92 will always be different depending on when you start the program, we can add 0x10 to get the desired value of 0x7d93. 

For example, our first run had 57 at 0x7d92. 0x57 + 0x10 would mean we need 0x7d93 to be 67.

Repeating `Enter` and `d` will help us watch as the value increases.

When 0x7d93 reaches that sum, we can `i` the value 58, and win the puzzle.

![magicwindow_win](https://github.com/coldwx/coldwx.github.io/assets/170867841/c5649a7f-9410-4470-ab79-38419f118ae0)

