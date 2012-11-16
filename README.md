ets_manager
===========

Separate management of ets tables to prevent lost data.

## Description

This library will allow tables to be created that will
have it's 'heir' as the manger process. It will then assign
the table to the calling process. If the process dies
unexpectedly the table returns to the manager process.

Add the library to your rebar config

```
{
  deps,
  [
   {ets_manager, "0.1.*", {git, "git://github.com/dipthegeezer/ets_manager.git", "HEAD"}},
   ....
  ]
}.

```
Then just use rebar to get the deps and use in your code.

```
./rebar get-deps

```

## Usage

### API

```
application:start(ets_manager).
P = spawn(
        fun() ->
            Name = foo,
            {ok, Name} = ets_manager:give_me(Name),
            receive
                {'ETS-TRANSFER', Tid, _Pid, new_table} -> some_function(Tid)
            end
         end.
    ),
%%kill and see if reassigns.
exit(P, zeds_dead_baby),
%%Reissue the table
Z = spawn(
        fun() ->
           Name = foo,
           {ok, Name} = ets_manager:give_me(Name),
           receive
               {'ETS-TRANSFER', Tid, _Pid, reissued} -> some_function(Tid)
           end
        end.
    ).

```
