##
##
##        Mod title:  Flood Protection for Guests
##
##      Mod version:  1.0
##  Works on FluxBB:  1.2.*
##     Release date:  2007-05-19
##           Author:  Smartys (smartys@punbb-hosting.com)
##
##      Description:  This mod allows administrators to limit how often Guests can make new posts.
##
##   Affected files:  post.php
##                    admin_groups.php
##                    include/functions.php
##
##       Affects DB:  Yes
##
##            Notes:  This mod tracks last post for Guests using IP addresses, so if you have many
##                    Guests visiting from the same IP, this may not be a good mod for you.
##
##       DISCLAIMER:  Please note that "mods" are not officially supported by
##                    FluxBb. Installation of this modification is done at your
##                    own risk. Backup your forum database and any and all
##                    applicable files before proceeding.
##
##


#
#---------[ 1. UPLOAD ]-------------------------------------------------------
#

install_mod.php to /


#
#---------[ 2. RUN ]----------------------------------------------------------
#

install_mod.php


#
#---------[ 3. DELETE ]-------------------------------------------------------
#

install_mod.php


#
#---------[ 4. OPEN ]---------------------------------------------------------
#

post.php


#
#---------[ 5. FIND (lines: 80-81) ]------------------------------------------
#

if (!$pun_user['is_guest'] && !isset($_POST['preview']) && $pun_user['last_post'] != '' && (time() - $pun_user['last_post']) < $pun_user['g_post_flood'])
	$errors[] = $lang_post['Flood start'].' '.$pun_user['g_post_flood'].' '.$lang_post['flood end'];


#
#---------[ 6. AFTER, ADD ]---------------------------------------------------
#

else if ($pun_user['is_guest'] && !isset($_POST['preview']) && $pun_user['o_last_post'] != '' && (time() - $pun_user['o_last_post']) < $pun_user['g_post_flood'])
	$errors[] = $lang_post['Flood start'].' '.$pun_user['g_post_flood'].' '.$lang_post['flood end'];


#
#---------[ 7. FIND (lines: 316-321) ]------------------------------------------
#

// If the posting user is logged in, increment his/her post count
if (!$pun_user['is_guest'])
{
	$low_prio = ($db_type == 'mysql') ? 'LOW_PRIORITY ' : '';
	$db->query('UPDATE '.$low_prio.$db->prefix.'users SET num_posts=num_posts+1, last_post='.$now.' WHERE id='.$pun_user['id']) or error('Unable to update user', __FILE__, __LINE__, $db->error());
}


#
#---------[ 8. AFTER, ADD ]---------------------------------------------------
#

else
{
	$db->query('UPDATE '.$db->prefix.'online SET last_post='.$now.' WHERE ident=\''.$db->escape(get_remote_address()).'\'') or error('Unable to update user', __FILE__, __LINE__, $db->error());
}


#
#---------[ 9. OPEN ]---------------------------------------------------------
#

include/functions.php


#
#---------[ 10. FIND (line: 125) ]---------------------------------------------
#

$result = $db->query('SELECT u.*, g.*, o.logged FROM '.$db->prefix.'users AS u INNER JOIN '.$db->prefix.'groups AS g ON u.group_id=g.g_id LEFT JOIN '.$db->prefix.'online AS o ON o.ident=\''.$remote_addr.'\' WHERE u.id=1') or error('Unable to fetch guest information', __FILE__, __LINE__, $db->error());


#
#---------[ 11. REPLACE WITH ]---------------------------------------------------
#

$result = $db->query('SELECT u.*, g.*, o.logged, o.last_post AS o_last_post FROM '.$db->prefix.'users AS u INNER JOIN '.$db->prefix.'groups AS g ON u.group_id=g.g_id LEFT JOIN '.$db->prefix.'online AS o ON o.ident=\''.$remote_addr.'\' WHERE u.id=1') or error('Unable to fetch guest information', __FILE__, __LINE__, $db->error());


#
#---------[ 12. OPEN ]---------------------------------------------------------
#

admin_groups.php


#
#---------[ 13. FIND (line: 125) ]---------------------------------------------
#

<?php if ($group['g_id'] != PUN_GUEST): ?>								<tr>
									<th scope="row">Edit subjects interval</th>
									<td>
										<input type="text" name="edit_subjects_interval" size="5" maxlength="5" value="<?php echo $group['g_edit_subjects_interval'] ?>" tabindex="23" />
										<span>Number of seconds after post time that users in this group may edit the subject of topics they've posted. Set to 0 to allow edits indefinitely.</span>
									</td>
								</tr>
								<tr>
									<th scope="row">Post flood interval</th>
									<td>
										<input type="text" name="post_flood" size="5" maxlength="4" value="<?php echo $group['g_post_flood'] ?>" tabindex="24" />
										<span>Number of seconds that users in this group have to wait between posts. Set to 0 to disable.</span>
									</td>
								</tr>
								<tr>
									<th scope="row">Search flood interval</th>
									<td>
										<input type="text" name="search_flood" size="5" maxlength="4" value="<?php echo $group['g_search_flood'] ?>" tabindex="25" />
										<span>Number of seconds that users in this group have to wait between searches. Set to 0 to disable.</span>
									</td>
								</tr>
<?php endif; ?>


#
#---------[ 14. REPLACE WITH ]---------------------------------------------------
#

<?php if ($group['g_id'] != PUN_GUEST): ?>								<tr>
									<th scope="row">Edit subjects interval</th>
									<td>
										<input type="text" name="edit_subjects_interval" size="5" maxlength="5" value="<?php echo $group['g_edit_subjects_interval'] ?>" tabindex="23" />
										<span>Number of seconds after post time that users in this group may edit the subject of topics they've posted. Set to 0 to allow edits indefinitely.</span>
									</td>
<?php endif; ?>								</tr>
								<tr>
									<th scope="row">Post flood interval</th>
									<td>
										<input type="text" name="post_flood" size="5" maxlength="4" value="<?php echo $group['g_post_flood'] ?>" tabindex="24" />
										<span>Number of seconds that users in this group have to wait between posts. Set to 0 to disable.</span>
									</td>
								</tr>
<?php if ($group['g_id'] != PUN_GUEST): ?>								<tr>
									<th scope="row">Search flood interval</th>
									<td>
										<input type="text" name="search_flood" size="5" maxlength="4" value="<?php echo $group['g_search_flood'] ?>" tabindex="25" />
										<span>Number of seconds that users in this group have to wait between searches. Set to 0 to disable.</span>
									</td>
								</tr>
<?php endif; ?>


#
#---------[ 15. SAVE/UPLOAD ]-------------------------------------------------
#