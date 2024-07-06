Welcome to the OpenHoldemBot Wiki

# Table of contents

* OpenHoldem
	* [Introduction](#introduction)
	* [The software](#the-software)
	* [Table maps](#table-maps)
	* [The bot-logic](#the-bot-logic)
	* [User-dll](#user-dll)
	* [Openholdem extensions](#openholdem-extensions)
	* [Real-money botting](#real-money-botting)
	* [Various topics](#various-topics)
* OpenPPL
	* [Introduction](#openppl-introduction)
	* [OpenPPL is The Language](#openppl-is-the-language)
	* [OpenPPL Symbols](#openppl-symbols)
	* [OpenPPL In Practice](#openppl-in-practice)
	* [If You Want To Buy...](#if-you-want-to-buy)

<br>

# OpenHoldem

<figure>
<img src="images/openholdem.jpg" width="200" height="200"/>
</figure>

## Introduction
* ### About OpenHoldem
	* [[Thank you to all volunteers!|thanks]]
	* [[What is openholdem|whatisopenholdem]]
	* [[What openholdem is not|whatopenholdemisnot]]
	* [[Winholdem heritage|winholdemheritage]]
	* [[A (few) word(s) of caution|caution]]
	* [[Openholdem references|openholdemreferences]]
* ### How OpenHoldem works
	* [[Connecting to a table|connecting_to_a_table]]
	* [[The heartbeat-cycle|heartbeat_cycle]]
	* [[The bot-logic (oh-script)|bot_logic_oh_script]]
	* [[The bot-logic (openppl)|bot_logic_openppl]]
	* [[Backup actions|backup_actions]]
	* [[Gecko, our default bot|gecko_our_default_bot]]
	* [[Hopper functions|hopper_functions]]
	* [[Log-files|log_files]]
	* [[Favourite failures|favourite_failures]]
* ### Installation and setup
	* [[Choosing an operating system|choosinganoperatingsystem]]
	* [[Configuring the operating system|configuringtheoperatingsystem]]
	* [[Installing the software|installingthesoftware]]
	* [[Openholdem directory|openholdemdirectory]]
	* [[Finding a table map|findingatablemap]]
	* [[Uninstallation|uninstallation]]
* ### License: gpl v3
	* [[License: gpl v3|LICENSE]]
	* [Frequently asked questions about the gnu licenses](https://www.gnu.org/licenses/gpl-faq.html)
<br>

## The software
* ### Gui
	* [[Main menu|main_menu]]
	* [[Toolbars|toolbars]]
	* [[Table display|tabledisplay]]
	* [[Statusbar|statusbar]]
* ### [[Preferences|preferences_title]]
	* [[Autoconnector|autoconnector]]
	* [[Autoplayer|autoplayer]]
	* [[Chat|chat]]
	* [[Configuration checks|configuration_checks]]
	* [[Debugging|debug]]
	* [[Dll extension|dllextension]]
	* [[Gui|gui]]
	* [[Hand history generator|handhistory_generator]]
	* [[Logging|logging]]
	* [[Pokertracker v4|pokertracker]]
	* [[Popup blocker|popup_blocker]]
	* [[Rebuy|rebuy]]
	* [[Replay frames|replayframes]]
	* [[Scraper|scraper]]
	* [[Stealth|obscurepreferences]]
	* [[Table positioner|table_positioner]]
	* [[Validator|validator]]
* ### Formula editor
	* [[Formula editor|formulaeditor]]
	* [[Hand list editor|hand_list_editor]]
	* [[Debug-tab|debug_tab]]
	* [[f$test|f_test]]
	* [[OpenPPL mode|openppl_mode]]
* ### ManualMode
	* [[ManualMode|manualmode]]
* ### OHreplay
	* [[Replay frames|replayframes]]
	* [[OHreplay|ohreplay]]
* ### Poker tracker
	* [[Poker tracker|pokertracker]]
<br>

## Table maps
* ### Introduction
	* [[Creating a table map|tablemap]]
	* [[Connecting openscrape to the table|connectopenscrapetothetable]]
	* [[Familiarizing yourself with openscrape|familiarizingwithopenscrape]]
* ### Scraping in practice
	* [[Seated, active, playing|seated_active_playing]]
	* [[Starting a new map|starting_a_new_map]]
	* [[Getting started|getting_started]]
	* [[Hashing images|hashing_images]]
	* [[Collecting fonts|fontcollectingexample]]
	* [[How to create a table map|howtocreateatablemap]]
	* [[Interface-buttons explained|interface_buttons_explained]]
	* [[Interpreting titletext|interpreting_titletext]]
* ### Expert tricks
	* [[Scraping &quot;10&quot; as T|scraping_10_as_ten]]
	* [[Scraping cents and dollars|scraping_dollars_and_cents]]
	* [[Scraping non-standard titles|scraping_non_standard_titles]]
	* [[Defining the game-type|setting_the_game_type_to_nolimit]]
	* [[Scraping fonts which are glued together|scraping_fonts_which_are_glued_together]]
	* [[Scraping blinking fonts and nameplates|scraping_blinkiung_fonts_and_nameplates]]
* ### Technical reference
	* [[Size records|sizerecords]]
	* [[Region records|regionrecords]]
	* [[Symbol records|symbolrecords]]
	* [[Font records|fontrecords]]
	* [[Hash point records|hashpointrecords]]
	* [[Hash records|hashrecords]]
	* [[Image records|imagerecords]]
<br>

## The bot-logic
* ### Built-in symbols
	* [[Symbols|symbols]]
	* [[The multiplexer-engine|multiplexer]]
	* [[Symbols poker tracker 4|symbols_pokertracker]]
	* [[Starting hand ranking|handrank]]
* ### OH-script
	* [[Syntax|oh_script_syntax]]
	* [[Hand lists|handlists]]
* ### OH-script explained
	* [[Prwin and versus|prwin_and_versus]]
	* [[Iterations and standard deviations for prwin|iterations_and_standard_deviations]]
	* [[ICM - the independent chip model|icm_explained]]
	* [[Pokerval explained|pokerval_explained]]
	* [[Memory symbols|memory_symbols]]
	* [[Rankbits|rankbits_explained]]
* ### Winngys hand strength library
	* [[Handstrength symbols|handstrength_library]]
	* [[Myhand explained|myhand_explained]]
<br>

## User-dll
* ### [[About User-dll|dll]]
* ### Dll-tutorial
	* [[Compiling the first dll|part_a_compiling_the_first_dll]]
	* [[Basic overview over the dll structure and examples|part_b_basic_overview]]
	* [[Setting up the *.ohf file for dll use|part_c_setting_up_ohf_for_dll]]
	* [[Using prw1326|part_e_using_prw1326]]
<br>

## Openholdem extensions
* [[Mouse-dll|mousedll]]
* [[Keyboard-dll|keyboarddll]]
* [[Pokertracker-query-definitions-dll|pokertracker_query_definitions_dll]]
* [[Chat|chat]]
<br>

## Real-money botting
### Automation
* [[Automation|automation]]
* [[Planning a hopper|planning_a_hopper]]
* [[Autoit - the language of choice|autoit]]
* [[Scout - an open-source hopper|scout]]
* [[Avoid race conditions|avoidraceconditions]]
* [[Setting process cpu afinity|settingprocesscpuafinity]]
* [[Setting windows classic theme|setting_windows_classic_theme]]
* [[Rebuys|rebuys]]
* [[Handling popup windows|handling_popup_windows]]
* [[Table changes in mtts|table_changes_in_tournaments]]
### Stealth
* [[About Stealth|stealth]]
* [[Your worst enemy|your_worst_enemy]]
* [[Casino security levels|colour_coded_pokersites]]
* [[The grand plan|the_grand_plan]]
* Single system configuration
	* [[Single system configuration|singlesystemconfiguration]]
	* [[Casino software|casinosoftware]]
	* [[Ninja setup|ninjafolders]]
	* [[Stealth preferences|obscurepreferences]]
	* [[Ini-files and registry|registry]]
	* [[Rootkits|rootkits]]
* Dual system configuration
	* [[Dual system configuration|dualsystemconfiguration]]
	* [[Virtualization|vmware]]
	* [[Bring|bring]]
<br>

## Various topics
* [[Outdated features and known incompatibilities|incompatibilities]]
* [[Contributing to the manual|contributing_to_the_manual]]
<br>
<br>

# OpenPPL

<figure>
<img src="images/openppl.jpg" width="200" height="200"/>
</figure>

## OpenPPL Introduction
* [[Contributors|contributors]]
* [[What Is OpenPPL?|what_is_openppl]]
* [[License - GPL|LICENSE]]
## OpenPPL is The Language
* [[Structure Of OpenPPL|structure_of_a_ppl_file]]
* [[Hand And Board Expressions|hand_and_board_expressions]]
* [[Actions|actions]]
* [[Handlists|handlists]]
* [[Mathematical Expression|mathematical_expressions]]
* [[User-Defined Variables|user_defined_variables]]
* [[Building Symbols On Your Own|building_symbols_on_your_own]]
## [[OpenPPL Symbols|openppl_symbols]]
* [[Betting-Action Symbols|bettingaction_symbols]]
* [[Betsizes And Stacksizes|betsizes_and_stacksizes]]
* [[Board Symbols|board_symbols]]
* [[Handstrength Symbols|handstrength_symbols]]
* [[Other Symbols|other_symbols]]
* [[Player Symbols|player_symbols]]
* [[PokerTracker Symbols|symbols_pokertracker]]
* [[Position Symbols|position_symbols]]
* [[Technical Symbols|technical_symbols]]
## OpenPPL In Practice
* [[Differences To Shanky-PPL|differences]]
* [[Gecko, Our Default-Bot|gecko_our_default_bot]]
* [[Other Default-Bots|other_demobots]]
* [[Debugging Your Code|debugging_your_code]]
* [[OpenPPL In Practice|openppl_in_practice]]
## [[If You Want To Buy...|if_you_want_to_buy]]

