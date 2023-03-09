##
##
##        Mod title:  SpamBarrier Mod
##
##      Mod version:  1.0.6
##  Works on FluxBB:  1.5.0, 1.5.1, 1.5.2
##     Release date:  2012-11-26
##           Author:  sklerder (sklerder@orange.fr)
##Based on works by:  - Reines (jamie@jamierf.co.uk),
##                    - blissend (blissend@gmail.com),
##                    - adaur (adaur.underground@gmail.com), 
##                    - Koos (pampoen10@yahoo.com),
##                    - Many others having, directly or indirectly, contributed
##
##      Description:  SpamBarrier Mod
##
##   Affected files:  profile.php
##                    register.php
##                    login.php
##                    lang/English/profile.php
##                    lang/English/register.php
##                    style/[Your_Style].css
##
##       Affects DB:  New table:
##                       'test_registrations'
##                    New options in 'config' table:
##                       'o_sb_check_sfs_register'
##                       'o_sb_sfs_api_key'
##                       'o_sb_check_hp'
##                       'o_sb_custom_field'
##                       'o_sb_custom_field_name'
##                       'o_sb_check_dnsbl_login'
##                       'o_sb_check_dnsbl_register'
##                       'o_sb_check_sfs_login'
##                       'o_sb_sfs_report'
##                       'o_sb_dnsbl_names'
##                       'o_sb_sfs_maxcheck'
##                       'o_sb_out_of_limit_ok'
##
##            Notes:  You can skip steps 4-18 and 30-38 if you do not need the
##                    ability to manually report spammers to the StopForumSpam
##                    database. Following these steps will add an extra option for
##                    admin when deleting users called "Delete user & report spam".
##                    Not tested on previous versions of FluxBB.
##
##       DISCLAIMER:  Please note that "mods" are not officially supported by
##                    PunBB. Installation of this modification is done at your
##                    own risk. Backup your forum database and any and all
##                    applicable files before proceeding.
##
##


#
#---------[ 1. UPLOAD ]-------------------------------------------------------
#

install_mod.php to /

files/include/spambarrier.php to /include/spambarrier.php
files/lang/* to /lang
files/plugins/AP_SpamBarrier.php to /plugins/AP_SpamBarrier.php

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

profile.php

#
#---------[ 5. FIND (line: 534) ]-------------------------------------------------
#

else if (isset($_POST['delete_user']) || isset($_POST['delete_user_comply']))

#
#---------[ 6. REPLACE WITH ]---------------------------------------------------
#

else if (isset($_POST['delete_user']) || isset($_POST['delete_spammer']) || isset($_POST['delete_user_comply']) || isset($_POST['delete_spammer_comply']))

#
#---------[ 7. FIND (line: 542) ]-------------------------------------------------
#

	$result = $db->query('SELECT group_id, username FROM '.$db->prefix.'users WHERE id='.$id) or error('Unable to fetch user info', __FILE__, __LINE__, $db->error());
	list($group_id, $username) = $db->fetch_row($result);

#
#---------[ 8. REPLACE WITH ]---------------------------------------------------
#

	$result = $db->query('SELECT group_id, username, email, registration_ip FROM '.$db->prefix.'users WHERE id='.$id) or error('Unable to fetch user info', __FILE__, __LINE__, $db->error());
	list($group_id, $username, $email, $registration_ip) = $db->fetch_row($result);

#
#---------[ 9. FIND (line: 548) ]-------------------------------------------------
#

	if (isset($_POST['delete_user_comply']))
	{

#
#---------[ 10. REPLACE WITH ]---------------------------------------------------
#

	if (isset($_POST['delete_user_comply']) || isset($_POST['delete_spammer_comply']))
	{
		if (isset($_POST['delete_spammer_comply']))
		{
			// Include the antispam library
			require PUN_ROOT.'include/spambarrier.php';

  			// Lets report the bastard!
  			sb_stopforumspam_report($registration_ip, $email, $username, '');
		}


#
#---------[ 11. FIND (line: 623) ]-------------------------------------------------
#

		redirect('index.php', $lang_profile['User delete redirect']);

#
#---------[ 12. REPLACE WITH ]---------------------------------------------------
#

		redirect('index.php', isset($_POST['delete_spammer_comply']) ? $lang_profile['Spammer delete redirect'] : $lang_profile['User delete redirect']);

#
#---------[ 13. FIND (line: 642) ]-------------------------------------------------
#

							<label><input type="checkbox" name="delete_posts" value="1" checked="checked" /><?php echo $lang_profile['Delete posts'] ?><br /></label>
						</div>
						<p class="warntext"><strong><?php echo $lang_profile['Delete warning'] ?></strong></p>

#
#---------[ 14. REPLACE WITH ]---------------------------------------------------
#

							<label><input type="checkbox" name="delete_posts" value="1" /><?php echo $lang_profile['Delete posts'] ?><br /></label>
						</div>
						<?php if (isset($_POST['delete_spammer'])): ?><p><?php echo $lang_profile['Delete spammer note'] ?></p><?php endif; ?>
						<p class="warntext"><strong><?php echo $lang_profile['Delete warning'] ?></strong></p>


#---------[ 15. FIND (line: 647) ]-------------------------------------------------
#

			<p class="buttons"><input type="submit" name="delete_user_comply" value="<?php echo $lang_profile['Delete'] ?>" /> <a href="javascript:history.go(-1)"><?php echo $lang_common['Go back'] ?></a></p>

#
#---------[ 16. REPLACE WITH ]---------------------------------------------------
#

			<p class="buttons"><input type="submit" name="<?php echo (isset($_POST['delete_spammer']) ? 'delete_spammer_comply' : 'delete_user_comply'); ?>" value="<?php echo $lang_profile['Delete'] ?>" /> <a href="javascript:history.go(-1)"><?php echo $lang_common['Go back'] ?></a></p>

#---------[ 17. FIND (line: 1572) ]-------------------------------------------------
#

							<input type="submit" name="delete_user" value="<?php echo $lang_profile['Delete user'] ?>" /> <input type="submit" name="ban" value="<?php echo $lang_profile['Ban user'] ?>" />

#
#---------[ 18. REPLACE WITH ]---------------------------------------------------
#

							<input type="submit" name="delete_user" value="<?php echo $lang_profile['Delete user'] ?>" /> <input type="submit" name="delete_spammer" value="<?php echo $lang_profile['Delete spammer'] ?>" /> <input type="submit" name="ban" value="<?php echo $lang_profile['Ban user'] ?>" />

#
#---------[ 19. OPEN ]---------------------------------------------------------
#

register.php

#
#---------[ 20. FIND ]-------------------------------------------------
#

// User pressed the cancel button

#
#---------[ 21. BEFORE, ADD ]---------------------------------------------------------
#

// HoneyPot Field Name
	if (($pun_config['o_sb_custom_field'] == 0))
		$reqfield = 'req_honeypot';
	else
		$reqfield=(!empty($pun_config['o_sb_custom_field_name'])) ? $pun_config['o_sb_custom_field_name'] : 'req_honeypot';
//End of HoneyPot Field Name

#
#---------[ 22. FIND (line: 89) ]-------------------------------------------------
#

	$username = pun_trim($_POST['req_user']);

#
#---------[ 23. REPLACE WITH ]---------------------------------------------------------
#

	$username = pun_trim($_POST[$reqfield]);

#
#---------[ 24. FIND (line: 192) ]-------------------------------------------------
#

	// Did everything go according to plan?

#
#---------[ 25. BEFORE, ADD ]---------------------------------------------------
#

//
// Begin of SpamBarrier check
//
	
	// Include the antispam library
	require PUN_ROOT.'include/spambarrier.php';

	$req_username = empty($username) ? pun_trim($_POST['req_user']) : $username;
	
	sb_check_spam_registration($req_username,$email1);

//
// End of SpamBarrier check
//

#
#---------[ 26. FIND (line: 284) ]-------------------------------------------------
#

$required_fields = array('req_user' => $lang_common['Username'], 'req_password1' => $lang_common['Password'], 'req_password2' => $lang_prof_reg['Confirm pass'], 'req_email1' => $lang_common['Email'], 'req_email2' => $lang_common['Email'].' 2');
$focus_element = array('register', 'req_user');

#
#---------[ 27. REPLACE WITH ]---------------------------------------------------------
#

$required_fields = array($reqfield => $lang_common['Username'], 'req_password1' => $lang_common['Password'], 'req_password2' => $lang_prof_reg['Confirm pass'], 'req_email1' => $lang_common['Email'], 'req_email2' => $lang_common['Email'].' 2');
$focus_element = array('register', $reqfield);

#
#---------[ 28. FIND (line: 303) ]-------------------------------------------------
#

						<label class="required"><strong><?php echo $lang_common['Username'] ?> <span><?php echo $lang_common['Required'] ?></span></strong><br /><input type="text" name="req_user" value="<?php if (isset($_POST['req_user'])) echo pun_htmlspecialchars($_POST['req_user']); ?>" size="25" maxlength="25" /><br /></label>

#
#---------[ 29. REPLACE WITH ]---------------------------------------------------------
#

						<label class="required usernamefield"><strong><?php echo $lang_register['If human'] ?></strong><br /><input type="text" name="req_user" value="" size="25" maxlength="25" /><br /></label>
						<label class="required"><strong><?php echo $lang_common['Username'] ?> <span><?php echo $lang_common['Required'] ?></span></strong><br /><input type="text" name="<?php echo $reqfield ?>" value="<?php if (isset($_POST[$reqfield])) echo pun_htmlspecialchars($_POST[$reqfield]); ?>" size="25" maxlength="25" /><br /></label>

#
#---------[ 30. OPEN ]----------------------------------------------
#

login.php

#
#---------[ 31. FIND ]----------------------------------------------
#

	// Remove this user

#
#---------[ 32. BEFORE, ADD ]---------------------------------------------------
#

//
// SpamBarrier BEGIN
//
	// Include the antispam library
  	require PUN_ROOT.'include/spambarrier.php';
	
	$membersIP= get_remote_address();

	sb_check_spam_login($membersIP,$form_username,$cur_user['email']);
	
//
// SpamBarrier END
//

#
#---------[ 33. OPEN ]---------------------------------------------------------
#

lang/English/profile.php

#
#---------[ 34. FIND (line: 131) ]-------------------------------------------------
#

'Delete user'				=>	'Delete user',

#
#---------[ 35. AFTER, ADD ]---------------------------------------------------
#

'Delete spammer'			=>	'Delete user &amp; report spam',
'Delete spammer note'		=>	'After deletion this user will be reported as a spammer. This is intended for reporting spam bots, <strong>not</strong> annoying users!',
'Spammer delete redirect'	=>	'User deleted and reported. Redirecting &hellip;',

#
#---------[ 36. OPEN ]---------------------------------------------------------
#

lang/French/profile.php

#
#---------[ 37. FIND ]---------------------------------------------------------
#

'Delete user'					=>	'Supprimer l\'utilisateur',
#
#---------[ 38. AFTER, ADD ]-------------------------------------------------
#

 
'Delete spammer'				=>	'Supprimer l\'utilisateur &amp; signaler le spammeur',
'Delete spammer note'			=>	'Après suppression, cet utilisateur sera signalé comme spammeur. Ceci est prévu pour signaler les robots de spam, <strong>pas</strong> pour les enquiquineurs !',
'Spammer delete redirect'		=>	'Utilisateur supprimé et signalé. Redirection …',


#
#---------[ 39. OPEN ]---------------------------------------------------------
#

lang/English/register.php

#
#---------[ 40. FIND (line: 33) ]-------------------------------------------------
#

'Confirm email'				=>	'Confirm email address',

#
#---------[ 41. AFTER, ADD ]---------------------------------------------------
#

'If human'					=>	'If you want to be reported as a spammer, please fill in this field!',


#
#---------[ 42. OPEN ]---------------------------------------------------------
#

lang/French/register.php

#
#---------[ 43. FIND ]---------------------------------------------------------
#


'Confirm email'				=>	'Confirmez votre adresse électronique',

#
#---------[ 44. AFTER, ADD ]-------------------------------------------------
#

'If human'					=>	'Si vous voulez être recensé comme spammeur, remplissez ce champ !',

#
#---------[ 45. OPEN ]---------------------------------------------------------
#

style/[YOUR_STYLE].css

#
#---------[ 46. AT THE END, ADD ]---------------------------------------------------
#

/* Something extra for the honeypot spam mod */

.pun .usernamefield {
	display: none;
}

#
#---------[ 47. SAVE, UPLOAD ]------------------------------------------------
#
