# OpenImages - COCO Label Mapping

## What for


If you want to evaluate **COCO-trained detectors** on **OpenImages** to measure **out-of-distribution (OOD) performance**, proper label mapping between OpenImage and Coco is needed, so I went through **all 601 OpenImages classes manually** and mapped them to align with COCO's 80 classes. Hope this saves you time!

## Statistics

After completing the mapping:

1. **All 80 COCO classes** were successfully matched to one or more OpenImages classes.

2. **65 OpenImages classes** are marked as **IGNORE** (should be excluded from evaluation)

3. **415 OpenImages classes** are marked as **OUT** (can be treated as OOD categories)



## The CSV File

The `openimages_coco_mapping_v2.csv` contains three columns that describe the mapping relationship:

**Column 1: OpenImages Class Names**

**Column 2: Status**
- Indicates how this class should be handled during evaluation
- Three possible values:
  - **IN** = Mapped to an in-distribution COCO class 
  - **IGNORE** = Should be ignored during evaluation (explanation in the next section)
  - **OUT** = Out-of-distribution class 

**Column 3: COCO Class Names**
- The corresponding COCO class name from the 80 COCO classes
- Only filled when Status = "IN"
- Empty for "IGNORE" and "OUT" classes

**Example rows:**
- `eagle, IN, bird` → Eagle in OpenImages maps to Bird in COCO
- `human arm, IGNORE,` → Human arm should be ignored during evaluation
- `axe, OUT,` → Axe can be treated as an OOD class during evaluation




## More Details

Here are the main issues I encountered while reviewing the classes, and why some need to be marked as "IGNORE":


### 1. Fine Classes in OpenImages, Coarse in COCO 

**Problem:**  
OpenImages sometimes break classes from coco into several more fine-grained classes.

**Solution:**  
Merge detailed OpenImages classes into the broader COCO class. The model was trained only on the broader class. And it would be unfair to say it is wrong when the model output a "Bird" when the real label is "Parrot"

**Examples:**

| OpenImages Class | → | COCO Class |
|------------------|---|------------|
| Eagle | → | Bird |
| Falcon | → | Bird |
| Owl | → | Bird |
| Parrot | → | Bird |
| Woodpecker | → | Bird |
| Man | → | Person |
| Woman | → | Person |
| Boy | → | Person |
| Girl | → | Person |
| Taxi | → | Car |
| Limousine | → | Car |



### 2. Objects in COCO that are Never Labeled 

**Problem:**  
Some objects appear a lot in COCO images, but COCO never labeled them. During training, the model learned to treat them as background.

**Solution:**  
Those should be deleted. The model was trained to ignore them as background. It would be unfair to test the model on those objects and expect the model to detect them.

**Examples:**
Footwear, Human_arm, Human_beard, Human...., Wheel,...



### 3. Different Names, Same Object 

**Problem:**  
Some OpenImages labels are almost the same object.

**Solution:**  
Merge them.

**Examples:**

| OpenImages Class | → | COCO Class |
|------------------|---|------------|
| Loveseat | → | Couch |
| Sofa_bed | → | Couch |
| Studio_couch | → | Couch |
| Jacuzzi | → | Bathtub |
| Submarine_sandwich | → | Sandwich |
| Ambulance | → | Truck |
| Bathroom_cabinet | → | Cabinet |
| Filing_cabinet | → | Cabinet |



### 4. Parent and Child Classes Both Exist 

**Problem:**  
OpenImages sometimes defines both:
- A general class (e.g., Flower)
- A specific type (e.g., Lily)

But in detection, one bounding box can only have one correct label.

**Solution:**  
Move everything to one consistent level. 

**Examples:**

| Parent in OpenImages |Child in OpenImages | → | Unified Label |
|-------------------|------------------|---|---------------|
| Hat | Cowboy_hat | → | Hat |
| Hat | Fedora | → | Hat |
| Hat | Sombrero | → | Hat |
| Clothing | Dress | → | Clothing |
| Clothing | Jacket | → | Clothing |
| Clothing | Jeans | → | Clothing |
| Clothing | Skirt | → | Clothing |
| Clothing | Suit | → | Clothing |
| Clothing | Miniskirt | → | Clothing |
| Clothing | Scarf | → | Clothing |
| Flower | Lily | → | Flower |
| Flower | Sunflower | → | Flower |
| Tree | Christmas_tree | → | Tree |
| Tree | Palm_tree | → | Tree |
| Tree | Willow | → | Tree |



### 5. Categories That Are Too Broad 

**Problem:**  
Some OpenImages labels are very vague and cover many different objects. They don't describe a clear, consistent visual object.

**Solution:**  
Remove these categories because they are too general to evaluate properly.

**Examples:**

Food, Drink, Animal, Baked_goods (parent category), Clothing (parent category), Furniture, Fruit, Kitchen_appliance, Kitchen_utensil, Land_vehicle, Medical_equipment, Musical_instrument (parent category), Office_supplies, Personal_care, Sports_equipment, Fashion_accessory, Home_appliance, Mammal, Watercraft, Weapon



## Note

All mappings in this repository were manually checked and verified,  there may still be cases I missed or edge cases that need refinement. If you find any issues or have suggestions for improvement, please feel free to raise them.


