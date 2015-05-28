Treatments
==========

If you want to assign participants to different treatment groups, you
can put the code in the subsession's ``before_session_starts`` method.
For example, if you want some participants to have a blue background to
their screen and some to have a red background, you would randomize as
follows:

.. code:: python

    def before_session_starts(self):
        # randomize to treatments
        for player in self.get_players():
            player.color = random.choice(['blue', 'red'])

(To actually set the screen color you would need to pass
``player.color`` to some CSS code in the template, but that part is
omitted here.)

If your game has multiple rounds, note that the above code gets executed
for each round. So if you want to ensure that participants are assigned
to the same treatment group each round, you should set the property at
the participant level, which persists across subsessions, and only set
it in the first round:

.. code:: python

    def before_session_starts(self):
        if self.round_number == 1:
            for p in self.get_players():
                p.participant.vars['color'] = random.choice(['blue', 'red'])

Then, in the view code, you can access the participant's color with
``self.player.participant.vars['color']``.


Choosing which treatment to play
--------------------------------

In the above example, players got randomized to treatments. This is
useful in a live experiment, but when you are testing your game, it is
often useful to choose explicitly which treatment to play. Let's say you
are developing the game from the above example and want to show your
colleagues both treatments (red and blue). You can create 2 session
types in settings.py that have the same keys to session type dictionary
, except the ``treatment`` key:

.. code:: python

    SESSION_TYPES = [
        {
            'name':'my_game_blue',
            # other arguments...

            'treatment':'blue',

        },
        {
            'name':'my_game_red',
            # other arguments...
            'treatment':'red',
        },
    ]

Then in the ``before_session_starts`` method, you can check which of the
2 session types it is:

.. code:: python

    def before_session_starts(self):
        for p in self.get_players():
            if 'treatment' in self.session.session_type:
                # demo mode
                p.color = self.session.session_type['treatment']
            else:
                # live experiment mode
                p.color = random.choice(['blue', 'red'])

Then, when someone visits your demo page, they will see the "red" and
"blue" treatment, and choose to play one or the other. If the demo
argument is not passed, the color is randomized.