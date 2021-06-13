# Sistemi Anti-Drone 

### Sistemi ynë detekton dronët përmes imazheve, videove ose burimeve të tjera në kohë reale.

## Kërkesat
+ Kërkesat e mjedisit referohen në file-n requirements.txt
+ Peshat e paratrajnuara të YOLO3 mund të shkarkohen nga nga file yolo3.weights
+ Testimi i objektit mund të bëhet edhe përmes fotografisë së një qeni. 

        $ python yolov3_detect_all.py -w yolo3.weights -i dog.jpg


+ Peshat e paratrajnuara duhet të vendosen në folderin rrënjë (root folder) në repository

## Dataset
+ Trajnimi i Yolov3 kërkon fotografitë me .xml fajlla në formatin PASCAL_VOC.
+ Folderi Dataset mund të shkarkohet në: https://drive.google.com/drive/folders/1blTGWhekfaA7lG-XWxa6LnCwqK52L1Qk. 
+ Përndryshe, nëse doni të krijoni datasetin tuaj, ndiqni këto hapa:


    1. Mbledh fotografi nga Kaggle Dataset apo Google Images
    2. Shkarkoni LabelImg (një mjet për shënimin e imazheve grafike) nga GitHub 
    3. Vendosni të gjitha fotografitë e folderit "dataset" në folderin "images", dhe xml fajlat në folderin "annots"

## Trajnimi 
### 1. Modifikimi i fajlit config.json
 + Specifikoni path-in e folderave 'images' dhe 'annots' në fushat "train_image_folder" dhe "train_annot_folder"
 + Fusha "labels" i liston etiketat (labels) mbi të cilat do të trajnohet modeli. Vetëm imazhet, të cilat e kanë etiketën 'uav', merren parasysh gjatë trajnimit.


        {
        "model" : {
            "min_input_size":       288,
            "max_input_size":       448,
            "anchors":              [55,69, 75,234, 133,240, 136,129, 142,363, 203,290, 228,184, 285,359, 341,260],
            "labels":               ["drone"]
        },

        "train": {
            "train_image_folder":   "F:/Drone/Drone_mira_dataset/images/", 
            "train_annot_folder":   "F:/Drone/Drone_mira_dataset/annots/",
            "cache_name":           "drone_train.pkl",

            "train_times":          8,     # numri i cikleve për nje set të trajnimit
            "pretrained_weights":   "",    # specifikon path-in e peshave të paratrajnuara, por është rregull të fillohet nga e para     
            "batch_size":           16,     # numri i fotografive që lexohen në secilin grup
            "learning_rate":        1e-4,  # shkalla bazë e të mesuarit (default Adam rate scheduler)
            "nb_epochs":            100,    # numri i epoches
            "warmup_epochs":        3,       
            "ignore_thresh":        0.5,
            "gpus":                 "0,1",

            "grid_scales":          [1,1,1],
            "obj_scale":            5,
            "noobj_scale":          1,
            "xywh_scale":           1,
            "class_scale":          1,

            "tensorboard_dir":      "logs",
            "saved_weights_name":   "drone.h5", # emri i fajlit në të cilin ruhet modeli i trajnuar
            "debug":                true    
        },

        "valid": {
            "valid_image_folder":   "C:/drone/valid_image_folder/",
            "valid_annot_folder":   "C:/drone/valid_annot_folder/",
            "cache_name":           "drone_valid.pkl",

            "valid_times":          1
        }
    }

### 2. Gjeneroni  anchors për datasetin
    $ python gen_anchors.py -c config.json
Kopjoni anchors të gjeneruara në terminal dhe vendosni në fajlin config.json 

###   3. Filloni procesin e trajnimit
    $ python train.py -c config.json
        
Deri në fund të këtij procesi, kodi do të shkruajë peshat e modelit më të mirë në fajllin uav_wh.h5 (apo ndonjë emër tjetër që specifikohet në fushën "save_weights_name" në config.json). Procesi i trajnimit ndalet kur humbja në grupin e vlerësimit nuk përmirësohet në 3 epoka radhazi.

### 4. Performoni detektim duke përdorur peshat e trajnuara në fotografi, grup të fotografive apo video kamerave
    $ python predict.py -c config.json -i /path/to/image/or/video/or/cam
 Për një fotografi përdorni:
        
    $ python predict.py -c config.json -i test.jpg
Për një video përdorni: 


    $ python predict.py -c config.json -i test.mp4
Për një detektim në kohë reale përdorni:
            
    $ python predict.py -c config.json -i webcam

    

## Evaluimi
Llogaritni mAP performancën e modelit të  definuar në  'saved_weights_name' në datasetin për validim që përcaktohet në fushat "valid_image_folder" dhe "valid_annot_folder" - 

    $ python evaluate.py -c config.json

##  Rezultatet

![5](https://user-images.githubusercontent.com/76743818/121774918-f6b34f00-cb84-11eb-8595-d045814295b5.jpg)
![6](https://user-images.githubusercontent.com/76743818/121774932-0337a780-cb85-11eb-9598-fcd156a4f6ac.jpg)
![0205](https://user-images.githubusercontent.com/76743818/121774939-0b8fe280-cb85-11eb-8c30-c7edc9ebcd17.jpg)
![0210](https://user-images.githubusercontent.com/76743818/121774941-0d59a600-cb85-11eb-880e-9542da4a5b0d.jpg)
![679](https://user-images.githubusercontent.com/76743818/121774944-0e8ad300-cb85-11eb-8923-605e7f84d474.jpg)
