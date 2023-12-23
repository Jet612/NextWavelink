Migrating
---------

Version **3** of nextwavelink has brought about many changes. This short guide should help you get started when moving
from version **2**.


**Some things may be missing from this page. If you see anything wrong or missing please make an issue on GitHub.**


Key Notes
=========

- Version **3** is now fully typed and compliant with pyright strict.
- Version **3** only works on Lavalink **v4+**.
- Version **3** now uses black and isort formatting, for consistency.
- Version **3** now better uses positional-only and keyword-only arguments in many places.
- Version **3** has better error handling with requests to your Lavalink nodes.
- Version **3** has Lavalink websocket completeness. All events have been implemented, with corresponding payloads.
- Version **3** has an experimental LFU request cache, saving you requests to YouTube and Spotify etc.


Removed
*******

- Spotify Extension
    - NextWavelink no longer has it's own Spotify Extension. Instead it has native support for LavaSrc and other source plugins.
    - Using LavaSrc with NextWavelink **3** is just as simple as using the built-in Lavalink search types.
- Removed all Track Types E.g. (YouTubeTrack, SoundCloudTrack)
    - NextWavelink **3** uses one class for all tracks. :class:`nextwavelink.Playable`.
    - :class:`nextwavelink.Playable` is a much easier and simple to use track that provides a powerful interface.
- :class:`nextwavelink.TrackSource` removed ``Local`` and ``Unknown``


Changed
*******

- All events have unique payloads.
- Playlists can not be used to search.
- :class:`nextwavelink.Playable` was changed significantly. Please see the docs for more info.
- :meth:`nextwavelink.Playable.search` was changed significantly. Please see the docs for more info.
- ``Node.id`` is now ``Node.identifier``.
- ``nextwavelink.NodePool`` is now ``nextwavelink.Pool``.
- :meth:`nextwavelink.Pool.connect` no longer requires the ``client`` keyword argument.
- :meth:`nextwavelink.Pool.get_node` the ``id`` parameter is now known as ``identifier`` and is positional-only. This parameter is also optional.
- :meth:`nextwavelink.Pool.fetch_tracks` was previously known as both ``.get_tracks`` and ``.get_playlist``. This method now returns either the appropriate :class:`nextwavelink.Playlist` or list[:class:`nextwavelink.Playable`]. If there is an error when searching, this method raises either a ``LavalinkException`` (The request failed somehow) or ``LavalinkLoadException`` there was an error loading the search (Request didn't fail).
- :meth:`nextwavelink.Queue.put_wait` now has an option to atomically add tracks from a :class:`nextwavelink.Playlist` or list[:class:`nextwavelink.Playable`]. This defaults to True. This currently checks if the track in the Playlist is Playable and if any errors occur will not add any tracks from the Playlist to the queue. IF set to ``False``, Playable tracks will be added to the Queue up until an error occurs or every track was successfully added.
- :meth:`nextwavelink.Queue.put_wait` and :meth:`nextwavelink.Queue.put` now return an int of the amount of tracks added.
- :meth:`nextwavelink.Player.stop` is now known as :meth:`nextwavelink.Player.skip`, though they both exist as aliases.
- ``Player.current_node`` is now known as :attr:`nextwavelink.Player.node`.
- ``Player.is_connected()`` is now known as :attr:`nextwavelink.Player.connected`.
- ``Player.is_paused()`` is now known as :attr:`nextwavelink.Player.paused`.
- ``Player.is_playing()`` is now known as :attr:`nextwavelink.Player.playing`.
- :meth:`nextwavelink.Player.connect` now accepts a timeout argument as a float in seconds.
- :meth:`nextwavelink.Player.play` has had additional arguments added. See the docs.
- ``Player.resume()`` logic was moved to :meth:`nextwavelink.Player.pause`.
- :meth:`nextwavelink.Player.seek` the ``position`` parameter is now positional-only, and has a default of ``0`` which restarts the track from the beginning.
- :meth:`nextwavelink.Player.set_volume` the ``value`` parameter is now positional-only, and has a default of ``100``.
- :attr:`nextwavelink.Player.autoplay` accepts a :class:`nextwavelink.AutoPlayMode` instead of a bool. AutoPlay has been changed to be more effecient and better with recomendations.
- :class:`nextwavelink.Queue` accepts a :class:`nextwavelink.QueueMode` in :attr:`nextwavelink.Queue.mode` for looping.
- Filters have been completely reworked. See: :class:`nextwavelink.Filters`
- ``Player.set_filter`` is now known as :meth:`nextwavelink.Player.set_filters`
- ``Player.filter`` is now known as :attr:`nextwavelink.Player.filters`


Added
*****

- :class:`nextwavelink.PlaylistInfo`
- :meth:`nextwavelink.Playlist.track_extras`
- :attr:`nextwavelink.Node.client` property was added. This is the Bot/Client associated with the node.
- :attr:`nextwavelink.Node.password` property was added. This is the password used to connect and make requests with this node.
- :attr:`nextwavelink.Node.heartbeat` property was added. This is the seconds as a float that aiohttp will send a heartbeat over websocket.
- :attr:`nextwavelink.Node.session_id` property was added. This is the Lavalink session ID associated with this node.
- :class:`nextwavelink.AutoPlayMode`
- :class:`nextwavelink.QueueMode`
- :meth:`nextwavelink.Node.close`
- :meth:`nextwavelink.Pool.close`
- :func:`nextwavelink.on_wavelink_node_closed`
- :meth:`nextwavelink.Node.send`
- :class:`nextwavelink.Search`
- LFU (Least Frequently Used) Cache for request caching.
- :meth:`nextwavelink.Node.fetch_info`
- :meth:`nextwavelink.Node.fetch_stats`
- :meth:`nextwavelink.Node.fetch_version`
- :meth:`nextwavelink.Node.fetch_player_info`
- :meth:`nextwavelink.Node.fetch_players`
- :attr:`nextwavelink.Playable.extras`
- :func:`nextwavelink.on_wavelink_extra_event`


Connecting
==========
Connecting in version **3** is similar to version **2**.


.. important::
    
    It is recommended to use nextcord ``setup_hook`` to connect your nodes.


.. code:: python3

    async def setup_hook(self) -> None:
        nodes = [nextwavelink.Node(uri="...", password="...")]

        # cache_capacity is EXPERIMENTAL. Turn it off by passing None
        await nextwavelink.Pool.connect(nodes=nodes, client=self, cache_capacity=100)

When your node connects you will recieve the :class:`nextwavelink.NodeReadyEventPayload` via :func:`nextwavelink.on_wavelink_node_ready`.


Searching and Playing
=====================
Searching and playing tracks in version **3** is different, though should feel quite similar but easier.


.. code:: python3

    # Search for tracks, with the default "ytsearch:" prefix.
    tracks: nextwavelink.Search = await nextwavelink.Playable.search("Ocean Drive")
    if not tracks:
        # No tracks were found...
        ...

    # Search for tracks, with a URL.
    tracks: nextwavelink.Search = await nextwavelink.Playable.search("https://www.youtube.com/watch?v=KDxJlW6cxRk")

    # Search for tracks, using Spotify and the LavaSrc Plugin.
    tracks: nextwavelink.Search = await nextwavelink.Playable.search("4b93D55xv3YCH5mT4p6HPn", source="spsearch")

    # Search for tracks, using Spotify and the LavaSrc Plugin, with a URL.
    # Notice we don't need to pass a source argument with URL based searches...
    tracks: nextwavelink.Search = await nextwavelink.Playable.search("https://open.spotify.com/track/4b93D55xv3YCH5mT4p6HPn")

    # Search for a playlist, using Spotify and the LavaSrc Plugin.
    # or alternatively any other playlist URL from another source like YouTube.
    tracks: nextwavelink.Search = await nextwavelink.Playable.search("https://open.spotify.com/playlist/37i9dQZF1DWXRqgorJj26U")


:class:`nextwavelink.Search` should be used to annotate your variables.
`.search` always returns a list[:class:`nextwavelink.Playable`] or :class:`nextwavelink.Playlist`, if no tracks were found
this method will return an empty ``list`` which should be checked, E.g:

.. code:: python3

    tracks: nextwavelink.Search = await nextwavelink.Playable.search(query)
    if not tracks:
        # No tracks were found...
        return

    if isinstance(tracks, nextwavelink.Playlist):
        # tracks is a playlist...
        added: int = await player.queue.put_wait(tracks)
        await ctx.send(f"Added the playlist **`{tracks.name}`** ({added} songs) to the queue.")
    else:
        track: nextwavelink.Playable = tracks[0]
        await player.queue.put_wait(track)
        await ctx.send(f"Added **`{track}`** to the queue.")


when playing a song from a command it is advised to check whether the Player is currently playing anything first, with
:attr:`nextwavelink.Player.playing`

.. code:: python3

    if not player.playing:
        await player.play(track)


You can skip adding any track to your history queue in version **3** by passing ``add_history=False`` to :meth:`nextwavelink.Player.play`.

NextWavelink **does not** advise using the :func:`nextwavelink.on_wavelink_track_end` event in most cases. Use this event only when you plan to
not use ``AutoPlay`` at all. Since version **3** implements :attr:`nextwavelink.AutoPlayMode.partial`, a setting which skips fetching and recommending tracks,
using this event is no longer recommended in most use cases.

To send track updates or do player updates, consider using :func:`nextwavelink.on_wavelink_track_start` instead.

.. code:: python3

    async def on_wavelink_track_start(self, payload: nextwavelink.TrackStartEventPayload) -> None:
        player: nextwavelink.Player | None = payload.player
        if not player:
            return

        original: nextwavelink.Playable | None = payload.original
        track: nextwavelink.Playable = payload.track

        embed: nextcord.Embed = nextcord.Embed(title="Now Playing")
        embed.description = f"**{track.title}** by `{track.author}`"

        if track.artwork:
            embed.set_image(url=track.artwork)

        if original and original.recommended:
            embed.description += f"\n\n`This track was recommended via {track.source}`"

        if track.album.name:
            embed.add_field(name="Album", value=track.album.name)

        # Send this embed to a channel...
        # See: simple.py example on GitHub.


.. note::

    Please read the AutoPlay section for advice on how to properly use version **3** with AutoPlay.


AutoPlay
========
Version **3** optimized AutoPlay and how it recommends tracks.

Available are currently **3** different AutoPlay modes.
See: :class:`nextwavelink.AutoPlayMode`

Setting :attr:`nextwavelink.Player.autoplay` to :attr:`nextwavelink.AutoPlayMode.enabled` will allow the player to fetch and recommend tracks
based on your current listening history. This currently works with Spotify, YouTube and YouTube Music. This mode handles everything including looping, and prioritizes the Queue 
over the AutoQueue.

Setting :attr:`nextwavelink.Player.autoplay` to :attr:`nextwavelink.AutoPlayMode.partial` will allow the player to handle the automatic playing of the next track
but **will NOT** recommend or fetch recommendations for playing in the future. This mode handles everything including looping.

Setting :attr:`nextwavelink.Player.autoplay` to :attr:`nextwavelink.AutoPlayMode.disabled` will stop the player from automatically playing tracks. You will need
to use :func:`nextwavelink.on_wavelink_track_end` in this case.

AutoPlay also implements error safety. In the case of too many consecutive errors trying to play a track, AutoPlay will stop attempting until manually restarted
by playing a track E.g. with :meth:`nextwavelink.Player.play`.


Pausing and Resuming
====================
Version **3** slightly changes pausing behaviour.

All logic is done in :meth:`nextwavelink.Player.pause` and you simply pass a bool (``True`` to pause and ``False`` to resume).

.. code:: python3

    await player.pause(not player.paused)


Queue
=====
Version **3** made some internal changes to :class:`nextwavelink.Queue`.

The most noticeable is :attr:`nextwavelink.Queue.mode` which allows you to turn the Queue to either,
:attr:`nextwavelink.QueueMode.loop`, :attr:`nextwavelink.QueueMode.loop_all` or :attr:`nextwavelink.QueueMode.normal`.

- :attr:`nextwavelink.QueueMode.normal` means the queue will not loop at all.
- :attr:`nextwavelink.QueueMode.loop_all` will loop every song in the history when the queue has been exhausted.
- :attr:`nextwavelink.QueueMode.loop` will loop the current track continuously until turned off or skipped via :meth:`nextwavelink.Player.skip` with ``force=True``.


Filters
=======
Version **3** has reworked the filters to hopefully be easier to use and feel more intuitive.

See: :class:`~nextwavelink.Filters`.
See: :attr:`~nextwavelink.Player.filters`
See: :meth:`~nextwavelink.Player.set_filters`
See: :meth:`~nextwavelink.Player.play`

**Some common recipes:**

.. code:: python3
    
    # Create a brand new Filters and apply it...
    # You can use player.set_filters() for an easier way to reset.
    filters: nextwavelink.Filters = nextwavelink.Filters()
    await player.set_filters(filters)


    # Retrieve the payload of any Filters instance...
    filters: nextwavelink.Filters = player.filters
    print(filters())


    # Set some filters...
    # You can set and reset individual filters at the same time...
    filters: nextwavelink.Filters = player.filters
    filters.timescale.set(pitch=1.2, speed=1.1, rate=1)
    filters.rotation.set(rotation_hz=0.2)
    filters.equalizer.reset()

    await player.set_filters(filters)


    # Reset a filter...
    filters: nextwavelink.Filters = player.filters
    filters.timescale.reset()

    await player.set_filters(filters)


    # Reset all filters...
    filters: nextwavelink.Filters = player.filters
    filters.reset()

    await player.set_filters(filters)


    # Reset and apply filters easier method...
    await player.set_filters()


Lavalink Plugins
================
Version **3** supports plugins in most cases without the need for any extra steps.

In some cases though you may need to send additional data.
You can use :meth:`nextwavelink.Node.send` for this purpose.

See the docs for more info.

