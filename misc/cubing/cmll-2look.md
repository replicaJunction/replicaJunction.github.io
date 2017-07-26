---
layout: page-nosidebar
title: "2-Look CMLL"
tags: ["cubing", "roux"]
---

* Table of Contents
{:toc}

# Introduction

The idea behind 2-look CMLL is to learn a few algorithms so you don't need to use a beginner method to solve CMLL, but not to learn an algorithm for each individual case ("full CMLL"). It's a good intermediate step - if you do opt to learn full CMLL later, these algorithms still solve certain cases.

Note that in these images, white is the U face and green is the F face.

# Look 1: Orientation

| Image | Name | Algorithm |
|-------|------|-----------|
|![Sune](/public/img/misc/cubing/sune.png)|[Sune](#sune)|[R U R' U R U2 R'](https://alg.cubing.net/?alg=R_U_R-_U_R_U2_R-&title=Sune&stage=CMLL&type=alg)|
|![Anti-Sune](/public/img/misc/cubing/antisune.png)|[Anti-Sune](#anti-sune)|[R' U' R U' R' U2 R](https://alg.cubing.net/?alg=R-_U-_R_U-_R-_U2_R&title=Anti-Sune&stage=CMLL&type=alg)|
|![Bowtie](/public/img/misc/cubing/bowtie.png)|[Bowtie](#bowtie)|[(F R' F' R) (U R U' R')](https://alg.cubing.net/?alg=(F_R-_F-_R)_(U_R_U-_R-)&title=Bowtie&stage=CMLL&type=alg)|
|![Headlights](/public/img/misc/cubing/headlights.png)|[Headlights](#headlights)|[F (R U R' U') F'](https://alg.cubing.net/?alg=F_(R_U_R-_U-)_F-&title=Headlights&stage=CMLL&type=alg)|
|![Chameleon](/public/img/misc/cubing/chameleon.png)|[Chameleon](#chameleon)|[(R U R' U') (R' F R F')](https://alg.cubing.net/?alg=(R_U_R-_U-)_(R-_F_R_F-)&title=Chameleon&stage=CMLL&type=alg)|
|![Dead Man](/public/img/misc/cubing/dead-man.png)|[Dead Man](#superman)|[F (RUR'U')2 F'](https://alg.cubing.net/?alg=F_(RUR-U-)2_F-&title=Dead%20Man&stage=CMLL&type=alg)|
|![Double Anti-Sune](/public/img/misc/cubing/double-anti-sune.png)|[Double Anti-Sune](#double-anti-sune)|[U R U2 R' U' R U R' U' R U' R'](https://alg.cubing.net/?alg=U_R_U2_R-_U-_R_U_R-_U-_R_U-_R-&title=Double%20Anti-Sune&stage=CMLL&type=alg)|

# Look 2: Permutation

| Image | Name | Algorithm |
|-------|------|-----------|
|![A2 - Adjacent Swap](/public/img/misc/cubing/a2-adjacent-swap.png)|[Adjacent Swap](#adjacent-swap)|[R' U L' U2 R U' R' U2 Lw2 x](https://alg.cubing.net/?alg=R-_U_L-_U2_R_U-_R-_U2_Lw2_x&title=Adjacent%20Swap&type=alg&stage=CMLL)|
|![A6 - Diagonal Swap](/public/img/misc/cubing/a6-diagonal-swap.png)|[Diagonal Swap](#adjacent-swap)|[R' U L' U2 R U' x' U L' U2 R U' R x'](https://alg.cubing.net/?alg=R-_U_L-_U2_R_U-_x-_U_L-_U2_R_U-_R_x-&title=Diagonal%20Swap&type=alg&stage=CMLL)|

# Notes

## Sune

Taught in almost every beginner method, most people will already know this one. To recognize Sune vs. Anti-Sune, look at the corner counter-clockwise from the solved piece. If the U color is pointing directly away from the solved piece, use Anti-Sune. If the U color is perpendicular to the solved piece, use Sune.

![Sune](/public/img/misc/cubing/sune.png)

In the example image above, the fully solved piece is in the UFL corner (easy to recognize because white is on top). The UFR corner tells us what to do - since white is pointing towards us, we use Sune. If the white color were facing the R face, this would be an Anti-Sune case.

## Anti-Sune

See the notes above for recognition between this and Sune.

Some people use an alternate version of Anti-Sune, so try both and use either. Note the targetting difference, though - in the version posted above, the solved piece goes in the UBL corner, while in this version, the solved piece goes in the UBR corner.

[R U2 R' U' R U' R](https://alg.cubing.net/?alg=R_U2_R-_U-_R_U-_R-&title=Anti-Sune%20(alt)&type=alg&stage=CMLL)

![Anti-Sune (alt)](/public/img/misc/cubing/antisune-alt.png)

## Bowtie

This one is easy if you think of it as two triggers:

1. [Hedgeslammer](https://www.speedsolving.com/wiki/index.php/Sledgehammer)
1. Inverse [sexy move](https://www.speedsolving.com/wiki/index.php/Sexy_move).

## Headlights

The "headlights" name actually comes from the two corners not visible in the image above. They're pointing "forward" in the same direction, like headlights on a car. Here's the other side of the cube:

![Headlights (reverse)](/public/img/misc/cubing/headlights-reverse.png)

Assuming you're right-handed, though, you'll probably want the alg shown above, since it uses the [sexy move](https://www.speedsolving.com/wiki/index.php/Sexy_move) which is very quick to execute.

## Chameleon

This one can be deceptive - at first, it looks like the headlights case above (hence the name). Check the two corners opposite from the oriented ones. If they face outwards in the same direction, it's a headlights case. If they face opposite from each other, you've got this case.

Here's the other side of the chameleon case, just to complement the case above:

![Chameleon (reverse)](/public/img/misc/cubing/chameleon-reverse.png)

Notice the corner with white on the F face. The other white is on the B face directly across from this one.

## Dead Man

The "headlights" are the poor man's legs, and the white pieces facing outward are his arms. Poor guy.

This is identical to [Headlights](#headlights), except that you do the sexy move twice.

## Double Anti-Sune

Recognize with two pairs of headlights. This one feels pretty foreign at first, but in my experience, it becomes pretty flowy pretty quickly. Be sure to use both hands - R' U' R flows much better with your left index finger on the U'.

## Adjacent Swap

I tend to remember this one in groups of four because of the U2 at the end of each: (R' U L U2) (R U' R' U2) Lw2 x

You can replace the Lw2 x at the end of this algorithm with R L to avoid a cube rotation, but R L is difficult to do without a regrip.

## Diagonal Swap

Although it starts out identical to the adjacent swap, I tend to remember this one in groups of three: (R' U L) (U2 R U') x' (U L' U2) (R U' R') x'

You can replace the R' x' at the end with Lw if you prefer.

# References

These algorithms came from [Waffle's 2LCMLL page](http://wafflelikescubes.webs.com/2lookcmll.htm). This was the tutorial I used to originally learn the Roux method, and I still think it's superb. Unfortunately, the images on that page don't work any more, though, which inspired me to create this.

The images on this page were generated from the image export feature on [alg.cubing.net](https://alg.cubing.net).