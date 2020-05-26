# 2Take1Menu Lua Function & Parameter Information
## special thanks to Sainan for the base API Documentation.

Should any script be called `autoexec.lua`, it will be executed when 2Take1Menu is injected.

## Types

### Feat

Instantiated by calling [`menu.add_feature`](#menu-add_feature).

- *boolean* `on`: this is only writable on toggle-type features without a callback function and parent-type features
- ***deprecated** boolean* `not_on`
- *string* `name`
- ***read only** Feat* `parent`
- *boolean* `threaded`: by default, this is `true`, resulting in the feature callback running in a new thread where errors will crash the game — setting this to `false` is highly recommended because the only downside is that `system.wait` won't work (`HANDLER_CONTINUE` and `os.time()` still work)
- ***read only*** *int* `type`
- *int* `min_i`: min value for int features
- *int* `max_i`: max value
- *int* `mod_i`: step size
- *int* `value_i`: value for int features — this has to be set after min_i, max_i, or mod_i
- *bool* `hidden`
- *function* `renderer`: Similar to the normal feature callback except that it's executed in the renderer thread where it can exclusively use `d3d` functions
- ***read only** function void* `toggle(Feat)`: non-static function that changes the currently open parent-type feature

**Only for `parent`s:**

- ***read only** vector<Feat>* `children`
- ***read only** int* `child_count`

### Regex

The constructor takes 1 string which represents the regex.

- *function RegexResult* `search(Regex, string)`: non-static function even though it takes a Regex as an argument, so you do `regexp.search(regexp, subject)`

### RegexResult

- *int* `count`
- *string[]* `matches`: 1-indexed in standard Lua fashion

### v2

Instantiated by calling `v2()`. The constructor takes no parameters.

- *float* `x`
- *float* `y`

### v3

Instantiated by calling `v3()`. The constructor takes no parameters.

- *float* `x`
- *float* `y`
- *float* `z` In GTA, this is the height. Has a hard limit lower bound of -200. There is no upper bound except that objects stop spawning around 2500.

## Constants

- `HANDLER_CONTINUE` = *int* `0`
- `HANDLER_POP` = *int* `1`

<h2><a name="events">Events</a></h2>

You can register event handlers with [`event.add_event_listener`](#event-add_event_listener)

### `exit` (ExitEvent)

- *int* `code`

### `chat` (ChatEvent)

- *int* `player`: author's session player ID
- *string* `body`: message contents

## Functions

<h3><a name="menu-add_feature"><i>Feat</i> <code>menu.add_feature(string name, string type, int parent = 0, function callback = nil)</code></a></h3>

Callback is optional, but if given, it will be called when the feature is clicked with the Feat as parameter, and its return value should be `HANDLER_POP` to pop the handler (default) or `HANDLER_CONTINUE` to call the handler again on the next frame.

**Possible types:**

- `action` (2048) — an option that can be clicked
- `parent` (512) — an option that can be clicked to show its sub-options
- `toggle` (1) — an option that can be toggled
- `action_value_i` (522) — an option with an integer value that can be clicked
- `autoaction_value_i` (1034) — an option with an integer value which will automatically execute the callback when the integer is changed
- `value_i` (11) — an option with an integer value that can be toggled

### *void* `menu.delete_feature(int id)`
### *bool* `menu.reset_features()`

Resets all known "Script Features" but this is not recommended as it will almost certainly crash the game.
Instead, try setting `.on = false` on a `parent`.

### *void* `menu.set_menu_can_navigate(bool toggle)`

Sets whether the menu responds to user keystrokes.

<h3><a name="event-add_event_listener"><i>int</i> <code>event.add_event_listener(string eventName, function callback)</code></a></h3>

The callback functions will get 1 event object parameter, containing all relevant event data, as described [above](#events).

Errors in your callback function won't crash the game, but they also won't appear above the map, so keep an eye on the log.

### *bool* `event.remove_event_listener(string eventName, int id)`

### *int* `hook.register_script_event_hook(function callback)`

Script event hooks are called with `(source, target, params, count)`

Note that the `params` table starts at `1`.

If the callback returns `false` script event will be blocked. Anything else will let the script event pass.

### *bool* `hook.remove_script_event_hook(int id)`

### *int* `player.player_id()`

Returns your current session player ID.

### *int* `player.get_player_ped(int session_player_id)`

Returns a ped entity ID.

### *void* `player.set_player_model(int model_hash)`
### *int* `player.get_player_group(int session_player_id)`

Shorthand for `ped.get_ped_group(player.get_player_ped(session_player_id))`.

Returns a group ID.

### *bool* `player.is_player_female(int session_player_id)`
### *bool* `player.is_player_friend(int session_player_id)`
### *bool* `player.is_player_playing(int session_player_id)`

Only seems to be `false` if the given player is dead.

### *bool* `player.is_player_free_aiming(int session_player_id)`
### *int* `player.get_entity_player_is_aiming_at(int session_player_id)`

Returns an entity ID. Use `entity.is_an_entity(int entity_id)` to find out if the player is aiming at something.

### *int* `player.get_personal_vehicle()`

Returns an entity ID. Use `entity.is_entity_a_vehicle(int entity_id)` to find out if the personal vehicle was found.

### *void* `player.set_player_visible_locally(int session_player_id, bool toggle)`
### *void* `player.set_local_player_visible_locally(bool toggle)`
### *void* `player.set_player_as_modder(int session_player_id, bool toggle)`

Has no effect on yourself.

### *void* `player.is_player_modder(int session_player_id)`
### *string|nil* `player.get_player_name(int session_player_id)`

Returns `nil` if the given session player ID is invalid, which means you can use this for iterating between 0 and 31 to find all valid session player IDs or rather all session players.

### *int* `player.get_player_scid(int session_player_id)`

Returns `-1` if the given session player ID is invalid, which means you can use this for iterating between 0 and 31 to find all valid session player IDs or rather all session players.

If you use this on yourself, it will return your spoofed SCID or `0` if not applicable.

### *int* `player.get_player_ip(int session_player_id)`
### *bool* `player.is_player_pressing_horn(int session_player_id)`

If you use this on yourself, it will only check if you are pressing the horn button regardless of whether your vehicle has a horn.

### *bool* `player.is_player_god(int session_player_id)`
### *int* `player.get_player_wanted_level(int session_player_id)`
### *int* `player.player_count()`
### *bool* `player.is_player_in_any_vehicle(int session_player_id)`
### *v3* `player.get_player_coords(int session_player_id)`
### *float* `player.get_player_heading(int session_player_id)`
### *float* `player.get_player_health(int session_player_id)`
### *float* `player.get_player_max_health(int session_player_id)`
### *float* `player.get_player_armour(int session_player_id)`
### *int* `player.get_player_from_ped(Ped ped)`
### *int* `player.get_player_team(int session_player_id)`
### *int* `player.get_player_vehicle(int session_player_id)`

Returns a vehicle entity ID.

### *bool* `player.is_player_vehicle_god(int session_player_id)`
### *bool* `player.is_player_host(int session_player_id)`
### *int* `player.get_host()`

Return's the session host's session player ID.

### *bool* `player.is_player_spectating(int session_player_id)`
### *int* `player.get_player_model(int session_player_id)`

Returns the model hash.

### *bool* `player.send_player_sms(int session_player_id, string message)`
### *int* `ped.create_ped(int type, int model_hash, v3 pos, float heading, bool isNetworked, bool unknown)`

**Possible types**:

- `0`: Micheal
- `1`: Franklin
- `2`: Trevor
- `4`: Male Civilian
- `5`: Female Civilian
- `6`: cop
- `20`: Medic
- `21`: Fireman
- `26`: Human
- `27`: SWAT
- `28`: Animal
- `29`: Army

[Ped Model List](https://wiki.rage.mp/index.php?title=Peds)

Returns the ped's entity ID.

### *int* `ped.clone_ped(int ped_id)`

Returns the ped's entity ID

### *bool* `ped.is_ped_in_any_vehicle(int ped_entity_id)`
### *bool* `ped.set_group_formation(int group_id, int formation)`

**Possible formations:**

- `0`: Stay in the vicinity of leader
- `1`: Circle around leader, facing leader
- `2`: Circle around leader, facing forward
- `3`: Line to left and right of leader

### *bool* `ped.set_ped_as_group_member(int ped_entity_id, int groupId)`
### *int* `ped.get_ped_group(int ped_entity_id)`

Returns a group ID.

### *int* `ped.get_group_size(int group)`
### *float* `ped.get_ped_health(int ped_entity_id)`
### *bool* `ped.set_ped_health(int ped_entity_id, float value)`
### *bool* `ped.is_ped_ragdoll(int ped_entity_id)`
### *bool* `ped.is_ped_a_player(int ped_entity_id)`
### *int* `ped.get_current_ped_weapon(int ped_entity_id)`

Returns the weapon's hash.

### *bool* `ped.set_ped_into_vehicle(int ped_entity_id, int vehicle_entity_id, int seat)`

**Possible Seats:**

-  `-3`: None
-  `-2`: Any
-  `-1`: Driver
-  `0`: Front right passenger
-  `1`: Rear left passenger
-  `2`: Rear right passenger
-  `3` -  `14`: Extra seats 1 - 12

See also: `vehicle.get_free_seat(int vehicle_entity_id)`

### *int* `ped.get_ped_drawable_variation(int ped_entity_id, int group)`
### *int* `ped.get_ped_texture_variation(int ped_entity_id, int group)`
### *int* `ped.get_ped_prop_index(int ped_entity_id, int group)`
### *int* `ped.get_ped_prop_texture_index(int ped_entity_id, int group)`
### *bool* `ped.set_ped_component_variation(int ped_entity_id, int component, int drawable, int texture, int pallette)`
### *bool* `ped.set_ped_prop_index(int ped_entity_id, int component, int drawable, int texture, int unknown)`
### *void* `ped.set_ped_can_switch_weapons(int ped_entity_id, bool toggle)`
### *bool* `ped.is_ped_shooting(int ped_entity_id)`
### *int* `ped.get_ped_bone_index(int ped_entity_id, int bone)`
### *bool* `ped.get_ped_bone_coords(v3* out, int ped_id, int bone_id_hash, v3 offset)`
### *int* `ped.get_ped_relationship_group_hash(int ped_entity_id)`
### *void* `ped.set_ped_relationship_group_hash(int ped_entity_id, int hash)`
### *int* `ped.get_vehicle_ped_is_using(int ped_entity_id)`

Returns an entity ID. Use `entity.is_entity_a_vehicle(int entity_id)` to find out if the ped is actually using a vehicle.

### *void* `ped.clear_all_ped_props(int ped_entity_id)`
### *int* `ped.clear_ped_tasks_immediately(int ped_entity_id)`
### *void* `ped.clear_ped_blood_damage(int ped_entity_id)`
### *bool* `ped.is_ped_in_vehicle(int ped_entity_id, int vehicle_entity_id)`
### *bool* `ped.is_ped_using_any_scenario(int ped_entity_id)`
### *bool* `ped.set_ped_to_ragdoll(int ped_entity_id, int time1, int time2, int type)`
### *bool* `ped.set_ped_can_ragdoll(int ped_entity_id, bool toggle)`
### *bool* `ped.can_ped_ragdoll(int ped_entity_id)`
### *bool* `ped.get_ped_last_weapon_impact(int ped_entity_id, v3& pos)`
### *bool* `ped.set_ped_combat_ability(int ped_entity_id, BYTE ability)`
```
0: CA_Poor  
1: CA_Average  
2: CA_Professional 
```

### *float* `ped.get_ped_max_health(int entity_id)`

For players, up to 328 is legit; up to with 986 Railgun resupply mission, 2500 as beast, and 2600 with ballistic armor.

### *bool* `ped.set_ped_max_health(int entity_id, float health)`
### *bool* `ped.resurrect_ped(int ped_entity_id)`
### *void* `ped.set_ped_combat_movement(int ped_entity_id, int type)`

```
0 - Stationary (Will just stand in place)  
1 - Defensive (Will try to find cover and very likely to blind fire)  
2 - Offensive (Will attempt to charge at enemy but take cover as well)  
3 - Suicidal Offensive (Will try to flank enemy in a suicidal attack) 
```


### *void* `ped.set_ped_combat_range(int ped_entity_id, int type)`

```
0: CR_Near  
1: CR_Medium  
2: CR_Far
```


### *void* `ped.set_ped_combat_attributes(int ped_entity_id, int attr, bool toggle)`

```

enum CombatAttributes

 {
 BF_CanUseCover = 0,
 BF_CanUseVehicles = 1,
 BF_CanDoDrivebys = 2,
 BF_CanLeaveVehicle = 3,
 BF_CanFightArmedPedsWhenNotArmed = 5,
 BF_CanTauntInVehicle = 20,
 BF_AlwaysFight = 46,
 BF_IgnoreTrafficWhenDriving = 52,
 BF_FreezeMovement = 292,
BF_PlayerCanUseFiringWeapons = 1424 };
 8 = ? 9 = ? 13 = ? 14 ?
```

### *void* `ped.set_ped_accuracy(int ped_entity_id, int accuracy)`

```
accuracy = 0-100, 100 being perfectly accurate  
```

### *int* `ped.get_number_of_ped_drawable_variations(int ped_id, int comp)`
### *int* `ped.get_number_of_ped_texture_variations(int ped_id, int comp, int draw)`
### *int* `ped.get_number_of_ped_prop_drawable_variations(int ped_id, int groupId)`
### *int* `ped.get_number_of_ped_prop_texture_variations(int ped_id, int groupId, int drawId)`
### *void* `ped.set_ped_random_component_variation(int ped_id)`
### *void* `ped.set_ped_default_component_variation(int ped_id)`
### *void* `ped.set_ped_movement_clipset(int ped_id, string szClipset)`
### *void* `ped.reset_ped_movement_clipset(int ped_id, bool unk0)`
### *bool* `ped.set_ped_config_flag(int ped_id, int flag, char value)`

### enum >  PedConfigFlags
```
{
  PED_FLAG_CAN_FLY_THRU_WINDSCREEN = 32,
        PED_FLAG_DIES_BY_RAGDOLL = 33,
    PED_FLAG_NO_COLLISION = 52,
        _PED_FLAG_IS_SHOOTING = 58,
        _PED_FLAG_IS_ON_GROUND = 60,
    PED_FLAG_NO_COLLIDE = 62,
 PED_FLAG_DEAD = 71,
        PED_FLAG_IS_SNIPER_SCOPE_ACTIVE = 72,
    PED_FLAG_SUPER_DEAD = 73,
        _PED_FLAG_IS_IN_AIR = 76,
  PED_FLAG_IS_AIMING = 78,
  PED_FLAG_DRUNK = 100,
        _PED_FLAG_IS_NOT_RAGDOLL_AND_NOT_PLAYING_ANIM = 104,
        PED_FLAG_NO_PLAYER_MELEE = 122,
  PED_FLAG_NM_MESSAGE_466 = 125,
    PED_FLAG_INJURED_LIMP = 166,
  PED_FLAG_INJURED_LIMP_2 = 170,
    PED_FLAG_INJURED_DOWN = 187,
  PED_FLAG_SHRINK = 223,
        PED_FLAG_MELEE_COMBAT = 224,
        _PED_FLAG_IS_ON_STAIRS = 253,
        _PED_FLAG_HAS_ONE_LEG_ON_GROUND = 276,
   PED_FLAG_NO_WRITHE = 281,
 PED_FLAG_FREEZE = 292,
    PED_FLAG_IS_STILL = 301,
        PED_FLAG_NO_PED_MELEE = 314,
        _PED_SWITCHING_WEAPON = 331,
  PED_FLAG_ALPHA = 410,
};
```
##** notes:**

(*) When flagId is set to 33 and the bool value to true, peds will die by starting ragdoll, so you should set this flag to false when you resurrect a ped.

When flagId is set to 62 and the boolvalue to false this happens:
` Ped is taken out of vehicle and can't get back in when jacking their empty vehicle.`
` If in a plane it falls from the sky and crashes.
 Sometimes peds vehicle continue to drive the route without its driver who's running after.`

(*)
JUMPING CHANGES  60,61,104 TO FALSE
BEING ON WATER CHANGES 60,61 TO FALSE AND 65,66,168 TO TRUE
FALLING CHANGES 60,61,104,276 TO FALSE AND TO 76 TRUE
DYING CHANGES 60,61,104,276* TO FALSE AND (NONE) TO TRUE
DYING MAKES 60,61,104 TO FALSE
BEING IN A CAR CHANGES 60,79,104 TO FALSE AND 62 TO TRUE

(*)Maximum value for flagId is 0x1AA (426) in b944.
ID 0xF0 (240) appears to be a special flag which is handled different compared to the others IDs.


### *bool* `ped.set_ped_ragdoll_blocking_flags(Ped ped, int flags)`
### *bool* `ped.reset_ped_ragdoll_blocking_flags(Ped ped, int flags)`
### *int* `vehicle.create_vehicle(int model_hash, v3 pos, float heading, bool networked, bool unknown)`

Returns the vehicle's entity ID.

### *void* `vehicle.set_vehicle_tire_smoke_color(int vehicle_entity_id, int r, int g, int b)`
### *int* `vehicle.get_ped_in_vehicle_seat(int vehicle_entity_id, int seat)`

Returns an entity ID. Use `entity.is_entity_a_ped(int entity_id)` to find out if a ped is actually in the given seat.

### *int* `vehicle.get_free_seat(int vehicle_entity_id)`
### *bool* `vehicle.is_vehicle_full(int vehicle_entity_id)`

Seems to always return `false`.

### *void* `vehicle.set_vehicle_stolen(int vehicle_entity_id, bool toggle)`
### *bool* `vehicle.set_vehicle_color(int vehicle_entity_id, BYTE p, BYTE s, BYTE pearl, BYTE wheel)`
### *string* `vehicle.get_mod_text_label(int vehicle_entity_id, int modType, int modValue)`
### *string* `vehicle.get_mod_slot_name(int vehicle_entity_id, int modType)`
### *int* `vehicle.get_num_vehicle_mods(int vehicle_entity_id, int modType)`
### *bool* `vehicle.set_vehicle_mod(int vehicle_entity_id, int modType, int modIndex, bool customTires)`

[List of Vehicle Mods](https://wiki.rage.mp/index.php?title=Vehicle_Mods)

### *int* `vehicle.get_vehicle_mod(int vehicle_entity_id, int modType)`
### *void* `vehicle.set_vehicle_extra(int vehicle_entity_id, int extra, bool toggle)`
### *bool* `vehicle.does_extra_exist(int vehicle_entity_id, int extra)`
### *bool* `vehicle.is_vehicle_extra_turned_on(int vehicle_entity_id, int extra)`
### *void* `vehicle.toggle_vehicle_mod(int vehicle_entity_id, int mod, bool toggle)`
### *void* `vehicle.set_vehicle_bulletproof_tires(int vehicle_entity_id, bool toggle)`
### *bool* `vehicle.is_vehicle_a_convertible(int vehicle_entity_id)`
### *bool* `vehicle.get_convertible_roof_state(int vehicle_entity_id)`
### *void* `vehicle.set_convertible_roof(int vehicle_entity_id, bool toggle)`
### *void* `vehicle.set_vehicle_indicator_lights(int vehicle_entity_id, int index, bool toggle)`
### *void* `vehicle.set_vehicle_brake_lights(int vehicle_entity_id, bool toggle)`
### *void* `vehicle.set_vehicle_can_be_visibly_damaged(int vehicle_entity_id, bool toggle)`
### *void* `vehicle.set_vehicle_engine_on(int vehicle_entity_id, bool toggle, bool instant, bool noAutoTurnOn)`
### *void* `vehicle.set_vehicle_fixed(int vehicle_entity_id)`
### *void* `vehicle.set_vehicle_deformation_fixed(int vehicle_entity_id)`
### *void* `vehicle.set_vehicle_undriveable(int vehicle_entity_id, bool toggle)`
### *bool* `vehicle.set_vehicle_on_ground_properly(int vehicle_entity_id)`
### *void* `vehicle.set_vehicle_forward_speed(int vehicle_entity_id, float speed)`
### *void* `vehicle.set_vehicle_number_plate_text(int vehicle_entity_id, string text)`
### *void* `vehicle.set_vehicle_door_open(int vehicle_entity_id, int doorIndex, bool loose, bool openInstantly)`
### *void* `vehicle.set_vehicle_doors_shut(int vehicle_entity_id, bool closeInstantly)`
### *bool* `vehicle.is_toggle_mod_on(int vehicle_entity_id, int index)`
### *void* `vehicle.set_vehicle_wheel_type(int vehicle_entity_id, int type)`
### *void* `vehicle.set_vehicle_number_plate_index(int vehicle_entity_id, int index)`
### *void* `vehicle.set_vehicle_tires_can_burst(int vehicle_entity_id, bool toggle)`
### *void* `vehicle.set_vehicle_tire_burst(int vehicle_entity_id, int index, bool onRim, float unknown)`
### *int* `vehicle.get_num_vehicle_mod(int vehicle_entity_id, int modType)`
### *bool* `vehicle.is_vehicle_engine_running(int vehicle_entity_id)`
### *void* `vehicle.set_vehicle_engine_health(int vehicle_entity_id, float health)`
### *bool* `vehicle.is_vehicle_damaged(int vehicle_entity_id)`
### *bool* `vehicle.is_vehicle_on_all_wheels(int vehicle_entity_id)`

See also: `entity.is_entity_in_air(int entity_id)`

### *bool* `vehicle.set_vehicle_doors_locked(int vehicle_entity_id, int lockStatus)`

[List of lock statuses](https://wiki.rage.mp/index.php?title=Vehicle::setDoorsLocked)

### *bool* `vehicle.set_vehicle_neon_lights_color(int vehicle_entity_id, int color)`
### *int* `vehicle.get_vehicle_neon_lights_color(int vehicle_entity_id)`
### *bool* `vehicle.set_vehicle_neon_light_enabled(int vehicle_entity_id, int index, bool toggle)`
### *bool* `vehicle.is_vehicle_neon_light_enabled(int vehicle_entity_id, int index, bool toggle)`
### *v3* `entity.get_entity_coords(int entity_id)`
### *bool* `entity.get_entity_offset_from_coords(v3& out, int entity_id, v3 coords)`
### *bool* `entity.get_entity_offset_from_entity(v3& out, int entity_id, int entity_id2)`
### *bool* `entity.set_entity_coords_no_offset(int entity_id, pos)`
### *v3* `entity.get_entity_rotation(int entity_id)`
### *bool* `entity.set_entity_rotation(int entity_id, v3 rot)`
### *bool* `entity.set_entity_heading(int entity_id, float heading)`
### *bool* `entity.set_entity_velocity(int entity_id, v3 velocity)`
### *v3* `entity.get_entity_velocity(int entity_id)`
### *bool* `entity.is_an_entity(int entity_id)`
### *bool* `entity.is_entity_a_ped(int entity_id)`
### *bool* `entity.is_entity_a_vehicle(int entity_id)`
### *bool* `entity.is_entity_an_object(int entity_id)`
### *bool* `entity.is_entity_dead(int entity_id)`
### *bool* `entity.is_entity_on_fire(int entity_id)`
### *bool* `entity.is_entity_visible(int entity_id)`
### *bool* `entity.is_entity_attached(int entity_id)`
### *bool* `entity.set_entity_visible(int entity_id, bool toggle)`
### *int* `entity.get_entity_type(int entity_id)`
### *bool* `entity.set_entity_gravity(int entity_id, bool gravity)`
### *void* `entity.apply_force_to_entity(int ped_entity_id, int forceType, float x, float y, float z, float rx, float ry, float rz, bool isRel, bool highForce)`

**Force Types:**

- `0`: MIN_FORCE
- `1`: MAX_FORCE_ROT
- `2`: MIN_FORCE_2
- `3`: MAX_FORCE_ROT_2
- `4`: FORCE_NO_ROT
- `5`: FORCE_ROT_PLUS_FORCE

### *int* `entity.get_entity_attached_to(int entity_id)`

Returns the ID of the entity that the given entity is attached to.

### *bool* `entity.detach_entity(int entity_id)`
### *int* `entity.get_entity_model_hash(int entity_id)`

Returns a model hash.

### *float* `entity.get_entity_heading(int entity_id)`
### *bool* `entity.attach_entity_to_entity(int subject_entity_id, int target_entity_id, int boneIndex, v3 offset, v3 rot, bool softPinning, bool collision, bool isPed, int vertexIndex, bool fixedRot)`
### *void* `entity.set_entity_as_mission_entity(int entity_id, bool toggle, char unknown)`
### *bool* `entity.set_entity_collision(int entity_id, bool toggle, bool physics, bool unknown)`
### *bool* `entity.set_entity_no_collsion_entity(int entity_id, int target_entity_id, bool unknown)`
### *bool* `entity.is_entity_in_air(int entity_id)`

See also: `vehicle.is_vehicle_on_all_wheels(int vehicle_entity_id)`

### *bool* `entity.set_entity_as_no_longer_needed(int entity_id)`
### *void* `entity.freeze_entity(int entity_id, bool toggle)`
### *void* `entity.set_entity_alpha(int entity_id, int alpha, bool skin)`
### *void* `entity.reset_entity_alpha(int entity_id)`
### *void* `entity.delete_entity(int entity_id)`
### *void* `entity.set_entity_god_mode(int entity_id, bool toggle)`
### *bool* `entity.get_entity_god_mode(int entity_id)`

Don't use this on player peds, instead, use `player.is_player_god(int session_player_id)`.

### *bool* `entity.is_entity_in_water(int entity_id)`
### *int* `object.create_object(int model_hash, v3 pos, bool networked, bool dynamic)`

Returns the object's entity ID.

### *void* `weapon.give_delayed_weapon_to_ped(int ped_entity_id, int weapon_hash, int time, bool equip_now)`

[Weapon Hash List](https://wiki.rage.mp/index.php?title=Weapons)

### *int* `weapon.get_weapon_tint_count(int weapon_hash)`
### *int* `weapon.get_ped_weapon_tint_index(int ped_entity_id, int weapon_hash)`
### *void* `weapon.set_ped_weapon_tint_index(int ped_entity_id, int weapon_hash, int weapon_tint_id)`

**Posible Weapon Tints:**

- `0`: Normal
- `1`: Green
- `2`: Gold
- `3`: Pink
- `4`: Army
- `5`: LSPD
- `6`: Orange
- `7`: Platinum

Note that this won't be accurate for all weapons, e.g. the widowmaker.

### *void* `weapon.give_weapon_component_to_ped(int ped_entity_id, int weapon_hash, int component_hash)`
### *void* `weapon.remove_all_ped_weapons(int ped_entity_id)`
### *void* `weapon.remove_weapon_from_ped(int ped_entity_id, int weapon_hash)`
### *bool|int* `weapon.get_max_ammo(int ped_entity_id, int weapon_hash)`
### *bool* `weapon.set_ped_ammo(int ped_entity_id, int weapon_hash, int ammo)`
### *bool* `streaming.request_model(int hash)`
### *bool* `streaming.has_model_loaded(int hash)`
### *bool* `streaming.set_model_as_no_longer_needed(int hash)`
### *bool* `streaming.is_model_in_cdimage(int hash)`
### *bool* `streaming.is_model_valid(int hash)`
### *bool* `streaming.is_model_a_plane(int hash)`
### *bool* `streaming.is_model_a_vehicle(int hash)`
### *bool* `streaming.is_model_a_heli(int hash)`
### *void* `streaming.request_ipl(string szName)`
### *void* `streaming.remove_ipl(string szName)`
### *void* `streaming.request_anim_set(string szName)`
### *bool* `streaming.has_anim_set_loaded(string szName)`
### *void* `streaming.request_anim_dict(string szName)`
### *bool* `streaming.has_anim_dict_loaded(string szName)`
### *void* `ui.notify_above_map(string message, string title, int colour)`

[List of Colours](https://wiki.rage.mp/index.php?title=Fonts_and_Colors#HUD_Colors)

### *void* `ui.set_new_waypoint(v2 coord)`
### *v2* `ui.get_waypoint_coord()`

### *int* `ui.get_entity_from_blip(int blip_id)`

Returns an entity ID.

### *int* `ui.get_blip_from_entity(int entity_id)`

Returns a blip ID, which seems to be `0` if the entity doesn't have a blip.

If the entity has multiple blips, only one blip ID will be returned. It's inconsistent whether an older or newer blip ID is returned, but every call has the same consistent result.

### *int* `ui.add_blip_for_entity(int entity_id)`

Returns a blip ID.

### *bool* `ui.set_blip_sprite(int blip_id, int sprite_id)`

[List of Sprite IDs](https://wiki.rage.mp/index.php?title=Blips#Blip_model)

### *bool* `ui.set_blip_colour(int blip_id, int colour)`

Changes the colour of the given blip's sprite; changing the sprite resets the colour.

[List of Colours](https://wiki.rage.mp/index.php?title=Blips#Blip_colors)

### *void* `ui.hide_hud_component_this_frame(int component_id)`

[List of Component IDs](https://wiki.rage.mp/index.php?title=HUD_Components)

### *void* `ui.hide_hud_and_radar_this_frame()`
### *string* `ui.get_label_text(string label)`
### *void* `ui.draw_rect(float x, float y, float width, float height, int r, int g, int b, int a)`

`x`, `y`, `width`, and `height` are `float`s between `0.0` and `1.0`.

### *void* `ui.draw_line(v3 pos1, v3 pos2, int r, int g, int b, int a)`
### *void* `ui.draw_text(string text, v2 pos)`

`pos.x` and `pos.y` are `float`s between `0.0` and `1.0`.

### *void* `ui.set_text_scale(float scale)`
### *void* `ui.set_text_color(int r, int g, int b, int a)`
### *void* `ui.set_text_font(int font)`
### *void* `ui.set_text_wrap(float start, float end)`
### *void* `ui.set_text_outline(bool b)`
### *void* `ui.set_text_centre(bool b)`
### *void* `d3d.draw_text(string text, v2 pos, v2 size, float scale, int color, int flags)`
### *int* `d3d.register_sprite(string path)`
### *void* `d3d.draw_sprite(int id, v2 pos, float scale, float rot, int color)`
### *v2* `d3d.get_sprite_origin(int id)`
### *v2* `d3d.get_sprite_size(int id)`
### *void* `d3d.draw_line(v2 start, v2 end, int size, int color)`
### *void* `d3d.draw_rect(v2 pos, v2 size, int color)`
### *v3* `cam.get_gameplay_cam_rot()`
### *v3* `cam.get_gameplay_cam_pos()`
### *float* `cam.get_gameplay_cam_relative_pitch()`
### *float* `cam.get_gameplay_cam_relative_yaw()`
### *int* `gameplay.get_hash_key(string in)`
### *void* `gameplay.display_onscreen_keyboard(string title, string default_text, int maxLength)`
### *bool* `gameplay.update_onscreen_keyboard()`
### *string* `gameplay.get_onscreen_keyboard_result()`
### *bool* `gameplay.is_onscreen_keyboard_active()`
### *void* `gameplay.set_override_weather(int weatherIndex)`
### *void* `gameplay.clear_override_weather()`
### *void* `gameplay.set_blackout(bool toggle)`
### *void* `gameplay.set_mobile_radio(bool toggle)`
### int `gameplay.get_game_state()`

Returns `0` if the player is in game.

### bool `gameplay.is_game_state(int game_state)`

Shorthand for `gameplay.get_game_state() == game_state`.

### *void* `gameplay.clear_area_of_objects(v3 coord, float radius, int flags)`
### *void* `gameplay.clear_area_of_vehicles(v3 coord, float radius, bool a3, bool a4, bool a5, bool a6, bool a7)`
### *void* `gameplay.clear_area_of_peds(v3 coord, float radius, bool a3)`
### *void* `gameplay.clear_area_of_cops(v3 coord, float radius, bool a3)`
### *bool* `fire.add_explosion(v3 pos, int type, bool isAudible, bool isInvis, float fCamShake, int owner_ped_id)`

[List of Explosions Types](https://wiki.rage.mp/index.php?title=Explosions)

### *int* `fire.start_entity_fire(int ped_entity_id)`

Returns the ped entity ID.

### *void* `fire.stop_entity_fire(int ped_entity_id)`
### *bool* `network.network_is_host()`

Returns `true` if the menu user is the session host.

### *bool* `network.request_control_of_entity(int entity_id)`
### *bool* `network.is_session_started()`
### *void* `cutscene.stop_cutscene_immediately()`
### *void* `cutscene.remove_cutscene()`

## *Controls*

### *bool* `controls.disable_control_action(int inputGroup, int control, bool disable)`
### *bool* `controls.is_control_just_pressed(int inputGroup, int control)`
### *bool* `controls.is_disabled_control_just_pressed(int inputGroup, int control)`
### *bool* `controls.is_control_pressed(int inputGroup, int control)`
### *bool* `controls.is_disabled_control_pressed(int inputGroup, int control)`

## *Input Groups:*

```
enum InputGroups
{
    INPUTGROUP_MOVE = 0,
  INPUTGROUP_LOOK = 1,
  INPUTGROUP_WHEEL = 2,
 INPUTGROUP_CELLPHONE_NAVIGATE = 3,
    INPUTGROUP_CELLPHONE_NAVIGATE_UD = 4,
 INPUTGROUP_CELLPHONE_NAVIGATE_LR = 5,
 INPUTGROUP_FRONTEND_DPAD_ALL = 6,
 INPUTGROUP_FRONTEND_DPAD_UD = 7,
  INPUTGROUP_FRONTEND_DPAD_LR = 8,
  INPUTGROUP_FRONTEND_LSTICK_ALL = 9,
   INPUTGROUP_FRONTEND_RSTICK_ALL = 10,
  INPUTGROUP_FRONTEND_GENERIC_UD = 11,
  INPUTGROUP_FRONTEND_GENERIC_LR = 12,
  INPUTGROUP_FRONTEND_GENERIC_ALL = 13,
 INPUTGROUP_FRONTEND_BUMPERS = 14,
 INPUTGROUP_FRONTEND_TRIGGERS = 15,
    INPUTGROUP_FRONTEND_STICKS = 16,
  INPUTGROUP_SCRIPT_DPAD_ALL = 17,
  INPUTGROUP_SCRIPT_DPAD_UD = 18,
   INPUTGROUP_SCRIPT_DPAD_LR = 19,
   INPUTGROUP_SCRIPT_LSTICK_ALL = 20,
    INPUTGROUP_SCRIPT_RSTICK_ALL = 21,
    INPUTGROUP_SCRIPT_BUMPERS = 22,
   INPUTGROUP_SCRIPT_TRIGGERS = 23,
  INPUTGROUP_WEAPON_WHEEL_CYCLE = 24,
   INPUTGROUP_FLY = 25,
  INPUTGROUP_SUB = 26,
  INPUTGROUP_VEH_MOVE_ALL = 27,
 INPUTGROUP_CURSOR = 28,
   INPUTGROUP_CURSOR_SCROLL = 29,
    INPUTGROUP_SNIPER_ZOOM_SECONDARY = 30,
    INPUTGROUP_VEH_HYDRAULICS_CONTROL = 31,
   MAX_INPUTGROUPS = 32,
 INPUTGROUP_INVALID = 33
};
0, 1 and 2 used in the scripts.
```

## *Input Control*

```
enum eInput
{
  INPUT_NEXT_CAMERA,
  INPUT_LOOK_LR,
  INPUT_LOOK_UD,
  INPUT_LOOK_UP_ONLY,
  INPUT_LOOK_DOWN_ONLY,
  INPUT_LOOK_LEFT_ONLY,
  INPUT_LOOK_RIGHT_ONLY,
  INPUT_CINEMATIC_SLOWMO,
  INPUT_SCRIPTED_FLY_UD,
  INPUT_SCRIPTED_FLY_LR,
  INPUT_SCRIPTED_FLY_ZUP,
  INPUT_SCRIPTED_FLY_ZDOWN,
  INPUT_WEAPON_WHEEL_UD,
  INPUT_WEAPON_WHEEL_LR,
  INPUT_WEAPON_WHEEL_NEXT,
  INPUT_WEAPON_WHEEL_PREV,
  INPUT_SELECT_NEXT_WEAPON,
  INPUT_SELECT_PREV_WEAPON,
  INPUT_SKIP_CUTSCENE,
  INPUT_CHARACTER_WHEEL,
  INPUT_MULTIPLAYER_INFO,
  INPUT_SPRINT,
  INPUT_JUMP,
  INPUT_ENTER,
  INPUT_ATTACK,
  INPUT_AIM,
  INPUT_LOOK_BEHIND,
  INPUT_PHONE,
  INPUT_SPECIAL_ABILITY,
  INPUT_SPECIAL_ABILITY_SECONDARY,
  INPUT_MOVE_LR,
  INPUT_MOVE_UD,
  INPUT_MOVE_UP_ONLY,
  INPUT_MOVE_DOWN_ONLY,
  INPUT_MOVE_LEFT_ONLY,
  INPUT_MOVE_RIGHT_ONLY,
  INPUT_DUCK,
  INPUT_SELECT_WEAPON,
  INPUT_PICKUP,
  INPUT_SNIPER_ZOOM,
  INPUT_SNIPER_ZOOM_IN_ONLY,
  INPUT_SNIPER_ZOOM_OUT_ONLY,
  INPUT_SNIPER_ZOOM_IN_SECONDARY,
  INPUT_SNIPER_ZOOM_OUT_SECONDARY,
  INPUT_COVER,
  INPUT_RELOAD,
  INPUT_TALK,
  INPUT_DETONATE,
  INPUT_HUD_SPECIAL,
  INPUT_ARREST,
  INPUT_ACCURATE_AIM,
  INPUT_CONTEXT,
  INPUT_CONTEXT_SECONDARY,
  INPUT_WEAPON_SPECIAL,
  INPUT_WEAPON_SPECIAL_TWO,
  INPUT_DIVE,
  INPUT_DROP_WEAPON,
  INPUT_DROP_AMMO,
  INPUT_THROW_GRENADE,
  INPUT_VEH_MOVE_LR,
  INPUT_VEH_MOVE_UD,
  INPUT_VEH_MOVE_UP_ONLY,
  INPUT_VEH_MOVE_DOWN_ONLY,
  INPUT_VEH_MOVE_LEFT_ONLY,
  INPUT_VEH_MOVE_RIGHT_ONLY,
  INPUT_VEH_SPECIAL,
  INPUT_VEH_GUN_LR,
  INPUT_VEH_GUN_UD,
  INPUT_VEH_AIM,
  INPUT_VEH_ATTACK,
  INPUT_VEH_ATTACK2,
  INPUT_VEH_ACCELERATE,
  INPUT_VEH_BRAKE,
  INPUT_VEH_DUCK,
  INPUT_VEH_HEADLIGHT,
  INPUT_VEH_EXIT,
  INPUT_VEH_HANDBRAKE,
  INPUT_VEH_HOTWIRE_LEFT,
  INPUT_VEH_HOTWIRE_RIGHT,
  INPUT_VEH_LOOK_BEHIND,
  INPUT_VEH_CIN_CAM,
  INPUT_VEH_NEXT_RADIO,
  INPUT_VEH_PREV_RADIO,
  INPUT_VEH_NEXT_RADIO_TRACK,
  INPUT_VEH_PREV_RADIO_TRACK,
  INPUT_VEH_RADIO_WHEEL,
  INPUT_VEH_HORN,
  INPUT_VEH_FLY_THROTTLE_UP,
  INPUT_VEH_FLY_THROTTLE_DOWN,
  INPUT_VEH_FLY_YAW_LEFT,
  INPUT_VEH_FLY_YAW_RIGHT,
  INPUT_VEH_PASSENGER_AIM,
  INPUT_VEH_PASSENGER_ATTACK,
  INPUT_VEH_SPECIAL_ABILITY_FRANKLIN,
  INPUT_VEH_STUNT_UD,
  INPUT_VEH_CINEMATIC_UD,
  INPUT_VEH_CINEMATIC_UP_ONLY,
  INPUT_VEH_CINEMATIC_DOWN_ONLY,
  INPUT_VEH_CINEMATIC_LR,
  INPUT_VEH_SELECT_NEXT_WEAPON,
  INPUT_VEH_SELECT_PREV_WEAPON,
  INPUT_VEH_ROOF,
  INPUT_VEH_JUMP,
  INPUT_VEH_GRAPPLING_HOOK,
  INPUT_VEH_SHUFFLE,
  INPUT_VEH_DROP_PROJECTILE,
  INPUT_VEH_MOUSE_CONTROL_OVERRIDE,
  INPUT_VEH_FLY_ROLL_LR,
  INPUT_VEH_FLY_ROLL_LEFT_ONLY,
  INPUT_VEH_FLY_ROLL_RIGHT_ONLY,
  INPUT_VEH_FLY_PITCH_UD,
  INPUT_VEH_FLY_PITCH_UP_ONLY,
  INPUT_VEH_FLY_PITCH_DOWN_ONLY,
  INPUT_VEH_FLY_UNDERCARRIAGE,
  INPUT_VEH_FLY_ATTACK,
  INPUT_VEH_FLY_SELECT_NEXT_WEAPON,
  INPUT_VEH_FLY_SELECT_PREV_WEAPON,
  INPUT_VEH_FLY_SELECT_TARGET_LEFT,
  INPUT_VEH_FLY_SELECT_TARGET_RIGHT,
  INPUT_VEH_FLY_VERTICAL_FLIGHT_MODE,
  INPUT_VEH_FLY_DUCK,
  INPUT_VEH_FLY_ATTACK_CAMERA,
  INPUT_VEH_FLY_MOUSE_CONTROL_OVERRIDE,
  INPUT_VEH_SUB_TURN_LR,
  INPUT_VEH_SUB_TURN_LEFT_ONLY,
  INPUT_VEH_SUB_TURN_RIGHT_ONLY,
  INPUT_VEH_SUB_PITCH_UD,
  INPUT_VEH_SUB_PITCH_UP_ONLY,
  INPUT_VEH_SUB_PITCH_DOWN_ONLY,
  INPUT_VEH_SUB_THROTTLE_UP,
  INPUT_VEH_SUB_THROTTLE_DOWN,
  INPUT_VEH_SUB_ASCEND,
  INPUT_VEH_SUB_DESCEND,
  INPUT_VEH_SUB_TURN_HARD_LEFT,
  INPUT_VEH_SUB_TURN_HARD_RIGHT,
  INPUT_VEH_SUB_MOUSE_CONTROL_OVERRIDE,
  INPUT_VEH_PUSHBIKE_PEDAL,
  INPUT_VEH_PUSHBIKE_SPRINT,
  INPUT_VEH_PUSHBIKE_FRONT_BRAKE,
  INPUT_VEH_PUSHBIKE_REAR_BRAKE,
  INPUT_MELEE_ATTACK_LIGHT,
  INPUT_MELEE_ATTACK_HEAVY,
  INPUT_MELEE_ATTACK_ALTERNATE,
  INPUT_MELEE_BLOCK,
  INPUT_PARACHUTE_DEPLOY,
  INPUT_PARACHUTE_DETACH,
  INPUT_PARACHUTE_TURN_LR,
  INPUT_PARACHUTE_TURN_LEFT_ONLY,
  INPUT_PARACHUTE_TURN_RIGHT_ONLY,
  INPUT_PARACHUTE_PITCH_UD,
  INPUT_PARACHUTE_PITCH_UP_ONLY,
  INPUT_PARACHUTE_PITCH_DOWN_ONLY,
  INPUT_PARACHUTE_BRAKE_LEFT,
  INPUT_PARACHUTE_BRAKE_RIGHT,
  INPUT_PARACHUTE_SMOKE,
  INPUT_PARACHUTE_PRECISION_LANDING,
  INPUT_MAP,
  INPUT_SELECT_WEAPON_UNARMED,
  INPUT_SELECT_WEAPON_MELEE,
  INPUT_SELECT_WEAPON_HANDGUN,
  INPUT_SELECT_WEAPON_SHOTGUN,
  INPUT_SELECT_WEAPON_SMG,
  INPUT_SELECT_WEAPON_AUTO_RIFLE,
  INPUT_SELECT_WEAPON_SNIPER,
  INPUT_SELECT_WEAPON_HEAVY,
  INPUT_SELECT_WEAPON_SPECIAL,
  INPUT_SELECT_CHARACTER_MICHAEL,
  INPUT_SELECT_CHARACTER_FRANKLIN,
  INPUT_SELECT_CHARACTER_TREVOR,
  INPUT_SELECT_CHARACTER_MULTIPLAYER,
  INPUT_SAVE_REPLAY_CLIP,
  INPUT_SPECIAL_ABILITY_PC,
  INPUT_CELLPHONE_UP,
  INPUT_CELLPHONE_DOWN,
  INPUT_CELLPHONE_LEFT,
  INPUT_CELLPHONE_RIGHT,
  INPUT_CELLPHONE_SELECT,
  INPUT_CELLPHONE_CANCEL,
  INPUT_CELLPHONE_OPTION,
  INPUT_CELLPHONE_EXTRA_OPTION,
  INPUT_CELLPHONE_SCROLL_FORWARD,
  INPUT_CELLPHONE_SCROLL_BACKWARD,
  INPUT_CELLPHONE_CAMERA_FOCUS_LOCK,
  INPUT_CELLPHONE_CAMERA_GRID,
  INPUT_CELLPHONE_CAMERA_SELFIE,
  INPUT_CELLPHONE_CAMERA_DOF,
  INPUT_CELLPHONE_CAMERA_EXPRESSION,
  INPUT_FRONTEND_DOWN,
  INPUT_FRONTEND_UP,
  INPUT_FRONTEND_LEFT,
  INPUT_FRONTEND_RIGHT,
  INPUT_FRONTEND_RDOWN,
  INPUT_FRONTEND_RUP,
  INPUT_FRONTEND_RLEFT,
  INPUT_FRONTEND_RRIGHT,
  INPUT_FRONTEND_AXIS_X,
  INPUT_FRONTEND_AXIS_Y,
  INPUT_FRONTEND_RIGHT_AXIS_X,
  INPUT_FRONTEND_RIGHT_AXIS_Y,
  INPUT_FRONTEND_PAUSE,
  INPUT_FRONTEND_PAUSE_ALTERNATE,
  INPUT_FRONTEND_ACCEPT,
  INPUT_FRONTEND_CANCEL,
  INPUT_FRONTEND_X,
  INPUT_FRONTEND_Y,
  INPUT_FRONTEND_LB,
  INPUT_FRONTEND_RB,
  INPUT_FRONTEND_LT,
  INPUT_FRONTEND_RT,
  INPUT_FRONTEND_LS,
  INPUT_FRONTEND_RS,
  INPUT_FRONTEND_LEADERBOARD,
  INPUT_FRONTEND_SOCIAL_CLUB,
  INPUT_FRONTEND_SOCIAL_CLUB_SECONDARY,
  INPUT_FRONTEND_DELETE,
  INPUT_FRONTEND_ENDSCREEN_ACCEPT,
  INPUT_FRONTEND_ENDSCREEN_EXPAND,
  INPUT_FRONTEND_SELECT,
  INPUT_SCRIPT_LEFT_AXIS_X,
  INPUT_SCRIPT_LEFT_AXIS_Y,
  INPUT_SCRIPT_RIGHT_AXIS_X,
  INPUT_SCRIPT_RIGHT_AXIS_Y,
  INPUT_SCRIPT_RUP,
  INPUT_SCRIPT_RDOWN,
  INPUT_SCRIPT_RLEFT,
  INPUT_SCRIPT_RRIGHT,
  INPUT_SCRIPT_LB,
  INPUT_SCRIPT_RB,
  INPUT_SCRIPT_LT,
  INPUT_SCRIPT_RT,
  INPUT_SCRIPT_LS,
  INPUT_SCRIPT_RS,
  INPUT_SCRIPT_PAD_UP,
  INPUT_SCRIPT_PAD_DOWN,
  INPUT_SCRIPT_PAD_LEFT,
  INPUT_SCRIPT_PAD_RIGHT,
  INPUT_SCRIPT_SELECT,
  INPUT_CURSOR_ACCEPT,
  INPUT_CURSOR_CANCEL,
  INPUT_CURSOR_X,
  INPUT_CURSOR_Y,
  INPUT_CURSOR_SCROLL_UP,
  INPUT_CURSOR_SCROLL_DOWN,
  INPUT_ENTER_CHEAT_CODE,
  INPUT_INTERACTION_MENU,
  INPUT_MP_TEXT_CHAT_ALL,
  INPUT_MP_TEXT_CHAT_TEAM,
  INPUT_MP_TEXT_CHAT_FRIENDS,
  INPUT_MP_TEXT_CHAT_CREW,
  INPUT_PUSH_TO_TALK,
  INPUT_CREATOR_LS,
  INPUT_CREATOR_RS,
  INPUT_CREATOR_LT,
  INPUT_CREATOR_RT,
  INPUT_CREATOR_MENU_TOGGLE,
  INPUT_CREATOR_ACCEPT,
  INPUT_CREATOR_DELETE,
  INPUT_ATTACK2,
  INPUT_RAPPEL_JUMP,
  INPUT_RAPPEL_LONG_JUMP,
  INPUT_RAPPEL_SMASH_WINDOW,
  INPUT_PREV_WEAPON,
  INPUT_NEXT_WEAPON,
  INPUT_MELEE_ATTACK1,
  INPUT_MELEE_ATTACK2,
  INPUT_WHISTLE,
  INPUT_MOVE_LEFT,
  INPUT_MOVE_RIGHT,
  INPUT_MOVE_UP,
  INPUT_MOVE_DOWN,
  INPUT_LOOK_LEFT,
  INPUT_LOOK_RIGHT,
  INPUT_LOOK_UP,
  INPUT_LOOK_DOWN,
  INPUT_SNIPER_ZOOM_IN,
  INPUT_SNIPER_ZOOM_OUT,
  INPUT_SNIPER_ZOOM_IN_ALTERNATE,
  INPUT_SNIPER_ZOOM_OUT_ALTERNATE,
  INPUT_VEH_MOVE_LEFT,
  INPUT_VEH_MOVE_RIGHT,
  INPUT_VEH_MOVE_UP,
  INPUT_VEH_MOVE_DOWN,
  INPUT_VEH_GUN_LEFT,
  INPUT_VEH_GUN_RIGHT,
  INPUT_VEH_GUN_UP,
  INPUT_VEH_GUN_DOWN,
  INPUT_VEH_LOOK_LEFT,
  INPUT_VEH_LOOK_RIGHT,
  INPUT_REPLAY_START_STOP_RECORDING,
  INPUT_REPLAY_START_STOP_RECORDING_SECONDARY,
  INPUT_SCALED_LOOK_LR,
  INPUT_SCALED_LOOK_UD,
  INPUT_SCALED_LOOK_UP_ONLY,
  INPUT_SCALED_LOOK_DOWN_ONLY,
  INPUT_SCALED_LOOK_LEFT_ONLY,
  INPUT_SCALED_LOOK_RIGHT_ONLY,
  INPUT_REPLAY_MARKER_DELETE,
  INPUT_REPLAY_CLIP_DELETE,
  INPUT_REPLAY_PAUSE,
  INPUT_REPLAY_REWIND,
  INPUT_REPLAY_FFWD,
  INPUT_REPLAY_NEWMARKER,
  INPUT_REPLAY_RECORD,
  INPUT_REPLAY_SCREENSHOT,
  INPUT_REPLAY_HIDEHUD,
  INPUT_REPLAY_STARTPOINT,
  INPUT_REPLAY_ENDPOINT,
  INPUT_REPLAY_ADVANCE,
  INPUT_REPLAY_BACK,
  INPUT_REPLAY_TOOLS,
  INPUT_REPLAY_RESTART,
  INPUT_REPLAY_SHOWHOTKEY,
  INPUT_REPLAY_CYCLEMARKERLEFT,
  INPUT_REPLAY_CYCLEMARKERRIGHT,
  INPUT_REPLAY_FOVINCREASE,
  INPUT_REPLAY_FOVDECREASE,
  INPUT_REPLAY_CAMERAUP,
  INPUT_REPLAY_CAMERADOWN,
  INPUT_REPLAY_SAVE,
  INPUT_REPLAY_TOGGLETIME,
  INPUT_REPLAY_TOGGLETIPS,
  INPUT_REPLAY_PREVIEW,
  INPUT_REPLAY_TOGGLE_TIMELINE,
  INPUT_REPLAY_TIMELINE_PICKUP_CLIP,
  INPUT_REPLAY_TIMELINE_DUPLICATE_CLIP,
  INPUT_REPLAY_TIMELINE_PLACE_CLIP,
  INPUT_REPLAY_CTRL,
  INPUT_REPLAY_TIMELINE_SAVE,
  INPUT_REPLAY_PREVIEW_AUDIO,
  INPUT_VEH_DRIVE_LOOK,
  INPUT_VEH_DRIVE_LOOK2,
  INPUT_VEH_FLY_ATTACK2,
  INPUT_RADIO_WHEEL_UD,
  INPUT_RADIO_WHEEL_LR,
  INPUT_VEH_SLOWMO_UD,
  INPUT_VEH_SLOWMO_UP_ONLY,
  INPUT_VEH_SLOWMO_DOWN_ONLY,
  INPUT_VEH_HYDRAULICS_CONTROL_TOGGLE,
  INPUT_VEH_HYDRAULICS_CONTROL_LEFT,
  INPUT_VEH_HYDRAULICS_CONTROL_RIGHT,
  INPUT_VEH_HYDRAULICS_CONTROL_UP,
  INPUT_VEH_HYDRAULICS_CONTROL_DOWN,
  INPUT_VEH_HYDRAULICS_CONTROL_LR,
  INPUT_VEH_HYDRAULICS_CONTROL_UD,
  INPUT_SWITCH_VISOR,
  INPUT_VEH_MELEE_HOLD,
  INPUT_VEH_MELEE_LEFT,
  INPUT_VEH_MELEE_RIGHT,
  INPUT_MAP_POI,
  INPUT_REPLAY_SNAPMATIC_PHOTO,
  INPUT_VEH_CAR_JUMP,
  INPUT_VEH_ROCKET_BOOST,
  INPUT_VEH_PARACHUTE,
  INPUT_VEH_BIKE_WINGS,
  INPUT_VEH_FLY_BOMB_BAY,
  INPUT_VEH_FLY_COUNTER,
  INPUT_VEH_TRANSFORM,
  INPUT_QUAD_LOCO_REVERSE,
  MAX_INPUTS,
  UNDEFINED_INPUT = -1
};
```

# --------------------------------

### *int* `graphics.get_screen_height()`
### *int* `graphics.get_screen_width()`
### *void* `time.set_clock_time(int hour, int minute, int second)`
### *int* `time.get_clock_hours()`
### *int* `time.get_clock_minutes()`
### *int* `time.get_clock_seconds()`
### *void* `ai.task_goto_entity(int entity_id, int target_entity_id, int duration, float distance, float speed)`
### *bool* `ai.task_combat_ped(int ped_entity_id, int target_ped_id, int a3, int a4)`

```
0 = None
1 = Unk
2 = CTaskVehicleRam
3 = CTaskVehicleBlock
4 = CTaskVehicleGoToPlane
5 = CTaskVehicleStop
6 = CTaskVehicleAttack
7 = CTaskVehicleFollow
8 = CTaskVehicleFleeAirborne
9= CTaskVehicleCircle
10 = CTaskVehicleEscort
15 = CTaskVehicleFollowRecording
16 = CTaskVehiclePoliceBehaviour
17 = CTaskVehicleCrash
```

### *Any* `ai.task_go_to_coord_by_any_means(int ped_entity_id, v3 coords, float speed, Any p4, bool p5, int walkStyle, float a7)`
### *bool* `ai.task_wander_standard(int ped_entity_id, float unknown, bool unknown)`
### *void* `ai.task_vehicle_drive_wander(int ped_entity_id, Vehicle vehicle, float speed, int driveStyle)`
### *void* `script.trigger_script_event(int eventId, int session_player_id, vector<int> params)`

Has no effect on yourself.

### *int* `script.get_host_of_this_script()`

Return's the script host's session player ID.

### *float* `script.get_global_f(int i)`
### *int* `script.get_global_i(int i)`
### *float* `script.get_local_f(int script_hash, int i)`
### *void* `script.set_local_f(int script_hash, int i, float value)`
### *int* `script.get_local_i(int script_hash, int i)`
### *void* `script.set_local_i(int script_hash, int i, int value)`
### *void* `decorator.decor_register(string name, int type)`
### *bool* `decorator.decor_exists_on(int entity_id, string decor)`
### *bool* `decorator.decor_remove(int entity_id, string decor)`
### *int* `decorator.decor_get_int(int entity_id, string name)`
### *bool* `decorator.decor_set_int(int entity_id, string name, int value)`
### *int* `interior.get_interior_from_entity(int entity_id)`

Returns the interior ID. `0` = Outside. [List of interior IDs.](https://forum.gta.world/en/index.php?/topic/3998-interior-list/)

### *Any* `interior.get_interior_at_coords_with_type(const v3& coords, string interiorType)`
### *void* `interior.enable_interior_prop(Any id, string prop)`
### *void* `interior.disable_interior_prop(Any id, string prop)`
### *void* `interior.refresh_interior(Any id)`
### *void* `audio.play_sound(int soundId, string audioName, string audioRef, bool p4, Any p5, bool p6)`
### *void* `audio.play_sound_frontend(int soundId, string audioName, string audioRef, bool p4)`
### *void* `audio.play_sound_from_entity(int soundId, string audioName, int entity_id, string audioRef)`
### *void* `audio.play_sound_from_coord(int soundId, string audioName, v3 pos, string audioRef, bool a5, int range, bool a7)`
### *void* `audio.stop_sound(int soundId)`
### *void* `system.wait(int milliseconds)`

This function only works within threaded feature callbacks.

<h2><a name="proddyutils">ProddyUtils Documentation</a></h2>

[ProddyUtils](https://github.com/Hampo/ProddyUtils/releases) is a C++ library that adds various util functions that are missing Lua 5.3, specifically created for 2Take1Menu.

Before you use any of these, you should put the latest `ProddyUtils.dll` into your `scripts/lib` folder and require it:

```Lua
if not ProddyUtils then
	if not package.cpath:find(os.getenv("APPDATA") .. "\\PopstarDevs\\2Take1Menu\\scripts\\lib\\?.dll", 1, true) then
		package.cpath = package.cpath .. ";" .. os.getenv("APPDATA") .. "\\PopstarDevs\\2Take1Menu\\scripts\\lib\\?.dll"
	end
	ProddyUtils = require("ProddyUtils")
end
```

### *bool* `ProddyUtils.CheckVersion(int Major, int Minor, int Build)`

***Since 1.3***

### *table* `ProddyUtils.GetVersion()`

***Since 1.3***

Returns ProddyUtils version in `{1, 3, 0}` format.

### *string* `ProddyUtils.Clipboard.GetText()`
### *bool* `ProddyUtils.Clipboard.SetText(string Text)`
### *bool* `ProddyUtils.IO.CreateDirectory(string Path)`
### *bool* `ProddyUtils.IO.DirExists(string Path)`

***Since 1.2***

### *bool* `ProddyUtils.IO.FileExists(string Path)`

***Since 1.2***

### *bool, bool* `ProddyUtils.IO.Exists(string Path)`

```Lua
local Exists, IsDir = ProddyUtils.IO.Exists(os.getenv("APPDATA") .. "\\PopstarDevs\\2Take1Menu\\scripts\\ProddyUtils Files")
```

### *table* `ProddyUtils.IO.GetFiles(string Path, string... Extensions)`

***Since 1.3***

### *bool* `ProddyUtils.IO.IterateDirectory(string Path, function Callback)`
### *table* `ProddyUtils.Keyboard.Keys`

***Since 1.4***

Table containing valid keys.

### *table* `ProddyUtils.Keyboard.DXKeys`

***Since 1.4***

Table containing valid DirectInput keys.

### *bool* `ProddyUtils.Keyboard.IsKeyPressed(ProddyUtils.Keyboard.Keys... Keys)`

***Since 1.4***

### *void* `ProddyUtils.Keyboard.KeyDown(ProddyUtils.Keyboard.DXKeys... Keys)`

***Since 1.4***

### *void* `ProddyUtils.Keyboard.KeyUp(ProddyUtils.Keyboard.DXKeys... Keys)`

***Since 1.4***

### *table* `ProddyUtils.MessageBox.Buttons`

- `OK` (1)
- `OKCancel` (2)
- `RetryCancel` (3)
- `YesNo` (4)
- `YesNoCancel` (5)

### *table* `ProddyUtils.MessageBox.DialogResult`

- `OK` (1)
- `Cancel` (2)
- `Retry` (3)
- `Yes` (4)
- `No` (5)

### *ProddyUtils.MessageBox.DialogResult* `ProddyUtils.MessageBox.Show(string Text, string Caption, ProddyUtils.MessageBox.Buttons Buttons)`

### *int* `ProddyUtils.OS.GetTimeNano()`

***Since 1.4***

### *int* `ProddyUtils.OS.GetTimeMicro()`

***Since 1.4***

### *int* `ProddyUtils.OS.GetTimeMillis()`

***Since 1.4***

<h2><a name="sainans-toolbox">Functions exposed by Sainan's Toolbox</a></h2>

[Sainan's Toolbox](.) exposes some functions to be used in your own scripts since 1.1.

Before you use any of these, you should check if Sainan's Toolbox is loaded:

```Lua
local SCRIPT_NAME = "Whatever your script is called"
if not sainans_toolbox then
	if sainans_toolbox_api_disabled then
		ui.notify_above_map(SCRIPT_NAME .. " requires the API to be enabled in Sainan's Toolbox.", SCRIPT_NAME, 6)
	else
		ui.notify_above_map(SCRIPT_NAME .. " is an extension for Sainan's Toolbox.", SCRIPT_NAME, 6)
	end
	return
end
```

As of 1.5, `.lua` files in the `script/Sainan's Toolbox Extensions/` folder will automatically be loaded with Sainan's Toolbox.

### *void* `sainans_toolbox.add_kick_event(hash)`

***Since 1.5***

Adds a kick event ID to be sent with invalid args when "Non-Host Kick" is used.

### *void* `sainans_toolbox.add_attachable(name, data)`

***Since 1.5***

Adds an attachable object for the "Attach" options.

`data` is a table containing all objects that are to be attached, each of which has 3 points of data:
- `1`: Model hash
- `2`: Attachment position as a vector, e.g. `{0, 0, 0}`
- `3`: Attachment rotation as a vector, e.g. `{0, 0, 0}`

See also: The `ATTACHABLES` table in Sainan's Toolbox.

### *int* `sainans_toolbox.v3_dist(v3 a, v3 b)`

***Since 1.6***

### *int* `sainans_toolbox.get_notify_colour()`

Returns the `colour` used by Sainan's Toolbox when calliing `ui.notify_above_map`.

### *void* `sainans_toolbox.on_register_player(function handler)`

Your `handler` will be called with `(string uid, int player_feature_id, int player_vehicle_feature_id)` as parameters when a player feature is registered.

Note that registering the player feature is not the same as the player joining the session, and depending on when your script is loaded, the player might not even be in session any more, resulting in `sainans_toolbox.uid_to_spid(uid)` being `nil`.

### *void* `sainans_toolbox.on_hide_player(function handler)`

***Since 1.6***

Your `handler` will be called with `(string uid)` when a user's feature is being hidden as a result of them leaving, which you might want to use to stop any user-related loops.

### *int* `sainans_toolbox.uid_to_spid(string uid)`

Converts a `uid` *string* from Sainan's Toolbox to a `session_player_id` *int* for use with 2Take1Menu's functions.

Note that one uid might have different session player id analogues during your script's lifespan.

### *string|nil* `sainans_toolbox.spid_to_uid(int session_player_id)`

***Since 1.6***

### *void* `sainans_toolbox.modder_detect(int session_player_id, string reason, float severity)`

***Since 1.6***

`severity` is a value between 0 and 1 indicating how severe your detection is, which should be equal to the percentage of people that trigger the detection being modders.

### *table* `sainans_toolbox.parse_uid(string uid)`

***Since 1.3***

Returns a table containing the following:

- *int* `scid`
- *string* `name`

### *int* `sainans_toolbox.get_all_players_feature_id()`
### *bool* `sainans_toolbox.is_exclude_self_on()`

Returns whether All Players > Exclude Self is on.

### *bool* `sainans_toolbox.is_exclude_friends_on()`

Returns whether All Players > Exclude Friends is on.

### *bool* `sainans_toolbox.is_clipboard_position_enabled()`

***Since 1.2***

### *void* `sainans_toolbox.add_clipboard_position_preset(string name, vector<int> pos)`

`pos` is not a *v3*:

```
sainans_toolbox.add_clipboard_position_preset("0 0 0", {0, 0, 0})
```

It's safe to call this function without first checking if clipboard position features are enabled, it will simply do nothing if disabled.

### *void* `sainans_toolbox.add_clipboard_position_preset_v3(string name, v3 pos)`

***Since 1.4***

It's safe to call this function without first checking if clipboard position features are enabled, it will simply do nothing if disabled.

### *v3|nil* `sainans_toolbox.get_clipboard_position()`

Returns the current clipboard position as a *v3* or `nil` if none is set.

It's safe to call this function without first checking if clipboard position features are enabled, it will always return `nil` if disabled.

### *void* `sainans_toolbox.set_clipboard_position(v3)`

***Since 1.3***

It's safe to call this function without first checking if clipboard position features are enabled, it will simply do nothing if disabled.

### *int* `sainans_toolbox.get_personal_vehicle_feature_id()`

***Since 1.5***

### *int* `sainans_toolbox.get_last_driven_vehicle_feature_id()`

***Since 1.5***

### *int|nil* `sainans_toolbox.get_last_driven_vehicle()`

***Since 1.5***

### *int* `sainans_toolbox.set_timeout(function callback, int ms)`

***Since 1.6***

Queues the given function to be called in the given amount of milliseconds.

Returns a timeout id.

### *void* `sainans_toolbox.clear_timeout(int timeout_id)`

***Since 1.6***

### *void* `sainans_toolbox.queue_entity_delete(int entity_id)`

***Since 1.6***

Tries to delete the given entity for 300 frames using various methods.
