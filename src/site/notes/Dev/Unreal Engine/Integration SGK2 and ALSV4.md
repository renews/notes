---
{"dg-publish":true,"dg-permalink":"tutorials/unreal-engine/integrating-sgkv2-with-alsv4","permalink":"/tutorials/unreal-engine/integrating-sgkv2-with-alsv4/","dgHomeLink":true,"dgPassFrontmatter":false}
---

# Integrating SGK2 with ALSV4
Well I took all the steps I used to integrate this 2 systems within a project I'm doing, hope this helps someone :) The SGK you can find in the Unreal Market, the ALS is in the link bellow.

ALSv4 Replicated with Blueprints: [https://github.com/Cesio137/ALSReplicated](https://draft.blogger.com/blog/post/edit/5343305929236502146/8530715374074848082#)

I have place links to images you can use as reference if you feel lost at a especific point :)

Let's go!

1. Find the character base of ALS
	1. Normal path: *ALS Folder -> Blueprint -> CharacterLogic -> **ALS_Base_CharacterBP***
	2. Open **Class Settings** and set the parent class to BP SGKMaster Character (This is inside the *Details* pane under *Class Options*)

2. Open *ALS Folder -> CharacterAssets -> MannequinSkeleton -> **ALS_AnimBP***

3. Open *SGKV2 Folder -> Animations* -> ***BP_ThirdPerson_Anim***

4. Copy from the ***BP_ThirdPerson_Anim*** to ***ALS_AnimBP***.
	1. ***Interfaces***
		1. Click class setting and on the details panel add *BP_SGKAnimation_Interface* on the implemented interfaces.
		2. Dont copy the content of interfaces for now.

	2. ***Variables***
		1. ALL
		
	3. ***Macros***
		1. IsServer
		2. IsLocallyControlled
	
	4. ***Functions***
		1. UpdateAOWeights
		2. CalculateAimOffset
		3. TurnInPlaceMontage
		4. SelectAnims

	5. **Event Graph**
		1.  Add a new graph clicking on the + icon, name it SGK [Image for reference](https://i.imgur.com/YVWmzep.png)
		2.  Copy everything inside event graph to this new graph
	
	6. **Interfaces again**
		1. Now copy the content of each implemented interface
		2. SGK Update Holdable Animations
		3. SGK End Chamber Montage
		4. SGK Update Animation State

	7. Check if the events copied from the BP_ThirdPerson_Anim to ALS_AnimBP point to the function inside ALS_AnimBP ( You can double click to see if it opens inside ALS_AnimBP )

	8. **Compile**

	9. **Open** the *ALS_AnimBP* AnimGraph (double click)
		1. Next to the Output Pose, right click type cache and click on the option ***new saved cache pose*** [Image for reference](https://i.imgur.com/iIq0kjg.png)
		2. Drag from the use cached pose AlsCache -> Type Slot, and change the slot to **UpperBodyPostAO** [Image for reference](https://i.imgur.com/qMAh5co.png) / details: [Image for reference](https://i.imgur.com/j9C8gZp.png)
		3. Use the cache created and move your output pose as such: [Image for reference](https://i.imgur.com/UDuOxa1.png)
		4. Configure your Layered blend per bone as such: [Image for reference](https://i.imgur.com/6Ul7ThM.png)

6. Open ***BP_MasterHoldable*** on the folder *SGKv2 Folder -> Blueprints -> Items -> HoldableItems* 
	1. Create a variable in the stance category, named ***AlsOverlay*** must be be the type ***ALS Overlay State***

7. Now edit the bp of your holdable items to match the similar item from ALS (*SGKv2 Folder -> Blueprints -> Items -> HoldableItems*) [Image for reference](https://i.imgur.com/hiSQwj0.png)
	1. Double click one of the BP on the folder listed above
	2. Search for ***als over***
	3. Select the type according to the item you on, compile and save
	4. Do this for every item.
	5. ALS dosent provide every type you maight need, so you will have to create other types yourself (like knife, building plan etc)


8. **On ALS_Base_CharacterBP** (*ALS Folder -> Blueprints -> CharacterLogic -> ALS_Base_CharacterBP*)
	1. Double click ReplicatedGraph
		1. Create a Custom Event "Server Set Overlay State" [Image for reference](https://i.imgur.com/OPfpZiw.png)
		2. Create another Custom Event "Multicast Overlay State" [Image for reference](https://i.imgur.com/Sx4Eprb.png)
		3. Connect them as [Image for reference](https://i.imgur.com/ZIg2FaA.png)
		4. ATTENTION FOR THE CUSTOM EVENT DETAILS!

9. **On BP_PlayerInventory**
	1. Create 2 Macros inside ALS Category to make it easier to find
		1. EquipAlsOverlay: 
		   Everything: [Image for reference](https://i.imgur.com/Ja29cpH.png)
		   Input params: [Image for reference](https://i.imgur.com/HJ7lj4W.png)
		   *To get the state, drag from the input Holdable and type get Als overlay*
		2. UnEquipAlsOverlay: [Image for reference](https://i.imgur.com/VnrlaaI.png)
			
	2. Double click the Function -> ***UseWeaponFromSlot***
		1. Go to comment group: "Play unequip montage, in no animation finish unequip"
			1. Disconnect the node ***Find Montage*** from the node ***NewHoldingItem***
			2. On the node ***NewHoldingItem*** call our macro ***UnequipAlsOverlay*** and connect it to the node ***Find Montage*** as follows: [Image for reference](https://i.imgur.com/tI2N6dv.png)
		2. Go to comment group: "If valid set new holding" 
			1. Place our macro ***EquipAlsOverlay*** at the end: [Image for reference](https://i.imgur.com/zEML7GF.png)
		3. Got to comment group: "Play equip montage, if none then finish equip"
			1. Disconnect the node ***Find Montage*** from the node ***New Holding Weapon Slot***
			2. Connect the node ***New Holding Weapon Slot***  to the node ***FindMontage*** as follows: [Image for reference](https://i.imgur.com/Xd5lh2F.png)
	
	3. Go to the function ***UnHoldItem***
		1. Got to the comment group: "If master range weapon exit aimed and reset fov then play unequip montage"
		2.  Disconnect the node from ***Find Montage***
		3. Add our Macro ***UnEquipeAlsOverlay*** and connect it to the ***FindMontage*** [Image for reference](https://i.imgur.com/KvxMB5D.png)
	
	4. Go  to the function ***AttemptToHoldItem***
		1. Go to the comment group: "Set actor current socket"
		2. Place our macro ***EquipeAlsOverlay*** before the ***New Holding Type*** node. [Image for reference](https://i.imgur.com/VWUiTzz.png)
		3. Go to the comment group: "Clear held item and call unequip animation and set to in action, if not animation finish unequip"
		4. Place our macro ***UnEquipAlsOverlay*** before the find montage node: [Image for reference](https://i.imgur.com/zjEkkyh.png)
	
	5. Go to the function ***DropItemFromSlot***
		1. Go to the comment group "Checks if item is being held and if it is clear highlighted quick slots and clear holdable veriable"
		2. In the last node and place our macro ***UnEquipAlsOverlay*** [Image for reference](https://i.imgur.com/5fgXH5V.png)
	6. Go to the function ***Unequip Weapon***
		1. Go to the comment group "Clear holdable variables and spawn held item into world then update weapon slot to empty"
		2. Place our macro ***UnEquipAlsOverlay*** before ***Holding Weapon Slot*** node [Image for reference](https://i.imgur.com/rbp5plF.png)
10. Copy the sockets from the SGK skeleton to the als skeleton
11. Now you have 3 choices, retarget the UE4_Mannequin to the ALS_Mannequin, delete the skeletal mesh inside unreal replace the references selecting the ALS one, and Fix Up redirections, or do the following.
12. Close your project.
13. go to your project folder at **/Content/Meshes/Skeletal** delete **UE4_Mannequin_Skeleton** file
14. Open the project again, unreal will ask to select a new skeleton for the animations, select the **ALS_Mannequin_Skeleton**
	1. This will happen multiple times, don't worry.*
	2. CHECK YOU WARNINGS on the output log to find meshes missing the new skeleton
15. For the remaining warnings, goto your Meshes -> Skeletal folder, right click the item you need, assign skeleton, select the **ALS_Mannequin_Skeleton** [Image for reference](https://i.imgur.com/V3NfYPj.png), theres no need to do this for  weapons.
16. Same thing for animation sequences
17. Same thing for animation montages
18. Animation montages sucks, so if one of them is not working, edit, click on the white track, and select the correct anim sequence, we can check them from a sgk reference project.
19. Set all Keybinding necessary in the project settings as the ALS require them
20. Remove meshs from *ALS_AnimMan_CharacterBP* -> ***Update Held Object*** if you want to use the meshs determined on SGKV2
21. (Optional) Remove unnecessary keybindings that SGK uses like crouch, jump etc
	


If you have errors like this when compiling [Image for reference](https://i.imgur.com/ndZR9R1.png) / [Image for reference](https://i.imgur.com/yTFBLQg.png), just right click the node and click in Refresh Node.

If getting errors like this [Image for reference](https://i.imgur.com/blWWd4p.png)
Check Your Graph in *ALS_AnimBP* SGK it has to have the custom event *UpdateSGK* like the image [Image for reference](https://i.imgur.com/VbmGbOL.png) and the update graph you have to call that event like this image [Image for reference](https://i.imgur.com/WaE6fiK.png)





#integration #sgkv2 #alsv4 #unrealengine #tutorial #gamedev #blogpost