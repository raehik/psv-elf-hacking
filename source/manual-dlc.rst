Installing NPS-format DLC manually (no PKGj)
============================================

If you're working on a fan translation, you may find that your game isn't yet
available on NoPayStation. If you're able to purchase the DLC, you may wish to
convert it to a shareable (not limited to one device) format that uses a fake
license, and can be loaded with NoNpDRM. This page walks through the steps to
install such "shared" DLC manually, without PKGj.

#. Obtain the DLC PKG and ``work.bin`` files. (``work.bin`` is NoNpDRM's fake
   license file, which you generate on a console with the DLC installed on.)
#. Run ``pkg2zip`` on the PKG to generate an installable ``.zip``.
#. Unzip the generated ``.zip`` to ``ux0:`` on your console. (It should add a
   folder at ``ux0:addcont/<GAME TITLE>/<DLC TITLE>``.)
#. Place ``work.bin`` at ``ux0:license/addcont/<GAME TITLE>/<DLC TITLE>/6488b73b912a753a492e2714e9b38bc7.rif``.
#. On your console, open VitaShell and press Triangle on the "home" screen
   (outside of ``ux0:`` etc.).
#. Select ``Refresh LiveArea``.

The DLC should be installed (though you may not get any prompts, and may see
``Refreshed 0 items.``). Open the game and check that everything is working as
expected.

You can also select ``Refresh license database``, which appears to put all
available game, DLC etc. licenses into a database which VitaShell can use to
regenerate licenses if they go missing. I don't *think* it's important for DLC
installation however.
