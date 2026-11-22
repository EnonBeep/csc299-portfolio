**Reflection**
*Developed CAT CAFE further*
# Q1.
`CLAUDE.md` line 3 says to "keep `spec.md` in sync", but fresh sessions added new rows instead of fixing old ones. Round 3 removed the checkboxes, but the R1 and R3 rows still said "ticking checkboxes" until I asked for a fix in round 6 (`handoff-round-6.md` line 12). Even then, the R9 row (`spec.md` line 18) still describes the ingredient tiles that round 6 removed, and R2 has two rows (lines 10–11) instead of one updated row. Round 4 also tried a large bash heredoc and it failed (`handoff-round-4.md` line 50), even though `handoff-round-2.md` line 51 and `handoff-round-3.md` line 50 already warned about it.

**What was missing:** the heredoc warning was only a note in the handoff files, not a rule in `CLAUDE.md`. Rounds 4, 5 and 6 all say "`CLAUDE.md` was not changed", while the same warnings were copied into every handoff. `CLAUDE.md` also needed a clearer spec rule: "when a round changes behaviour, rewrite every spec row it affects."

# Q2.
Round 4 changed R5 (a wrong drink loses 1 coin, never below 0) and R9 (ingredients are pictures dragged into a takeaway cup with a lid).

**What broke:** The `TestRound3` serve tests had to put the lid on before serving (`docs/decisions/0004-...` line 21). The old R5 test,`test_R5_coins_only_when_correct`, didn't break: its wrong drink was served at 0 coins, which stays 0 under both rules (0004 line 16).

**Did the tests catch it?** For R9, yes. For R5, no: the old test was too weak to notice the change. Only the new test `test_R5_wrong_drink_loses_one_coin_but_never_below_zero`, checks it. Also, `/api/serve` with an explicit `ingredients` list skips the lid rule (`handoff-round-4.md` line 24), so some old tests never go through the lid step.

**Does the decision record explain it?** 
Yes, 0004 line 8 gives the reason ("a wrong drink had no consequence, so there was no reason to becareful") and the alternatives (keeping R5, losing more than 1 coin, keeping text names), and lines 13–29 list every test that changed. Its last line, "because the user asked for these changes", is weak on its own, but line 8 already gives the real reason.

# Q3.
In lines 415-517 in the `test_cafe.py` Claude wrote this in round 5 section:
`def test_R4_mixing_does_not_change_matching(self):`
    `for name, recipe in cafe.RECIPES.items():`
        `self.assertTrue(cafe.drink_matches(recipe, name))`
The name makes it sound like it checks that mixing the liquids (R12) doesn't mess up how drinks get matched (R4). But it never actually mixes anything. It doesn't call `mix_cup` or put anything in the cup. All it does is check that each recipe matches itself, which was already true back in round 1. So if mixing broke, this test would still pass.

# Q4.
Commit `1f6cbe8`, "Build first version of cat cafe game with spec, tests and docs", put the whole first build in one commit. I would split it into separate commits for the spec, the tests, the game code, and the README, so the history shows the tests came before the code. From round 2 on I asked for that in my prompts, and the commits were split (`79c23a3` spec, `d774ff6` tests, `7a05e4e` code).

# Q5.
Function chosen `make_server` in `cafe.py`
I thought this function chreated the port for the game hosted on the computer under the port 8000.

The assistance informaed me it build a web server that only my computer can reach (127.0.0.1) on port 8000 by defualy and then connects it to the game with make handler. It doesnt start the server main() does.

I was right to thing that it hosts the game on my computer on port 8000. However I was wrong that it creates the port as it only makes the server that listens on a port and it doesnt run until main() calls on it.