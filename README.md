# Sistemi Anti-Drone për zbulimin dhe neutralizimin e UAV

## Sistemi ynë zbulon dronët përmes imazheve, videove ose burimeve të tjera në kohë reale.

## Kërkesat
    + Kërkesat e mjedisit referohen në file-n requirements.txt
    + Peshat e paratrajnuara të YOLO3 mund të shkarkohen nga nga file zolo3.weights
    + Testimi i objektit mund të bëhet edhe përmes fotografisë së një qeni.
     -> File shkarkues "$ python yolo3_one_file_to_detect_them_all.py -w yolo3.weights -i dog.jpg "
    + Peshat e paratrajnuara duhet të vendosen në folderin rrënjë (root folder) të depozitës (repository)

## Dataset
    + Trajnimi i Yolov3 përkrah fotografitë e formatit .xml në formatin PASCAL_VOC.
    + Folderi Datasetmund të shkarkohet.
    + Folderi Dataset mund edhe të krijohet, por duhet ndjekur disa hapa:
      -> Mbledh fotografi nga Kaggle Dataset apo Google Images
      -> Një mjet për shënim grafik të një imazhi (si psh Labelimg), 
      -> Vendosja e të gjitha fotografive të folderit "dataset" në folderin "images", dhe xml file-s në folderin "annots"

## Trajnimi 
### 1. Modifikimi i file-s config.js
    + Specifikimi i path-it të folderave images dhe annots në fushat "train_image_folder" dhe "train_annot_folder"
    + "labels" listat e cilësive dhe labels duhet të trajnohen. Vetëm imazhet, të cilat kanë etiketa të listuara, futen në rrjet.

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

            "train_times":          8,     # numri i cikleve përmes grupit të trajnimit
            "pretrained_weights":   "",    # specifikon rrugën e peshave të paratrajnuara, por është mirë të fillohet nga e para     
            "batch_size":           16,     # numri i fotografive që lexohen në secilin grup
            "learning_rate":        1e-4,  # shkalla bazë e të mesuarit të paracaktuar
            "nb_epochs":            100,    # numri i epokave
            "warmup_epochs":        3,       
            "ignore_thresh":        0.5,
            "gpus":                 "0,1",

            "grid_scales":          [1,1,1],
            "obj_scale":            5,
            "noobj_scale":          1,
            "xywh_scale":           1,
            "class_scale":          1,

            "tensorboard_dir":      "logs",
            "saved_weights_name":   "drone.h5", # emri i model file-t në të cilin është ruajtur modeli ynë i trajnuar
            "debug":                true    # turn on/off the line to print current confidence,position,size,class losses,recall
        },

        "valid": {
            "valid_image_folder":   "C:/drone/valid_image_folder/",
            "valid_annot_folder":   "C:/drone/valid_annot_folder/",
            "cache_name":           "drone_valid.pkl",

            "valid_times":          1
        }
    }

### 2. Gjeneron spiranca për të dhënat
    $ python gen_anchors.py -c config.json
    Kopjimi i spirancave (anchors) të gjeneruara të shtypura në terminal në vendosjen e tyre në filen config.js 

###   3. Fillimi i procesit të trajnimit
    $ python train.py -c config.json
    Deri në fund të këtij procesi, kodi do të shkruajë peshat e modelit më të mirë ne filen drone.h5 (apo ndonjë emër tjetër që specifikohet ne filen "save_weights_name" config.json). Procesi i trajnimit ndalet kur humbja në grupin e vlerësimit nuk përmirësohet në 3 epoka radhazi.

### 4. Kryerja e detektimit duke përdorur peshat e trajnuara në fotografi, grup të fotografive apo kamerave në internet.
    $ python predict.py -c config.json -i /path/to/image/or/video/or/cam
    Për përdorimin e fotove: $ python predict.py -c config.json -i test.jpg
    Për përdorimin e videove: $ python predict.py -c config.json -i test.mp4
    Për përdorimin e një fushe në kohë reale: $ python predict.py -c config.json -i webcam

    
}
# Evaluimi
    Llogarit MAP performancën të modelit të përcaktuar të definuar në fushat "valid_image_folder" dhe "valid_annot_folder" - $ python evaluate.py -c config.json

#  Rezultati

