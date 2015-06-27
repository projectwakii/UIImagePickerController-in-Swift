# BodyCompare-UIImagePicker
An example using UIImagePickerController, written in Swift 2.0. Allows an image to be chosen from either camera (if available) or photo libraries. 

# Explanation 
There are two components to this. The first is setting up everything we need to be able to use the UIImagePickerController, and the second is creating an action sheet that pops up with different options that allows you to choose whether you use the camera or photo library to select your image. 

# UIImagePickerController 
First, we make sure that the correct subclasses are part of our ViewController class. We need these three delegates: 

``` 
class ViewController: UIViewController, UIImagePickerControllerDelegate, UINavigationControllerDelegate {
...
}
```

We then create an instance of UIImagePickerController, making sure that its delegate is connected to our ViewController. 
```
class ViewController: UIViewController, UIImagePickerControllerDelegate, UINavigationControllerDelegate {

    let imagePicker = UIImagePickerController()

    override func viewDidLoad() {
        super.viewDidLoad()
        imagePicker.delegate = self
    }
    
}
```

At the bottom of the class, we set up the necessary things that handle what happens when the image is actually chosen (or not chosen). It is important to dismiss the view controller or it will just stay up permanently, analogous to a young man who accidentally took too much viagra. 

```
    // MARK: - Image Picker Delegates
    //The UIImagePickerController is a view controller that gets presented modally. When we select or cancel the picker, it runs the delegate, where we handle the case and dismiss the modal. 
    
    
    func imagePickerController(picker: UIImagePickerController, didFinishPickingImage image: UIImage, editingInfo: [String : AnyObject]?) {
        print("finished picking image")
    }
    
    
    func imagePickerController(picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : AnyObject]) {
        //handle media here i.e. do stuff with photo
        
        print("imagePickerController called")
        
            let chosenImage = info[UIImagePickerControllerOriginalImage] as! UIImage
            imageButtonImage.image = chosenImage
        dismissViewControllerAnimated(true, completion: nil)
    }
    
    func imagePickerControllerDidCancel(picker: UIImagePickerController) {
        //what happens when you cancel
        //which, in our case, is just to get rid of the photo picker which pops up
        dismissViewControllerAnimated(true, completion: nil)
    }
```

To present the UIImagePickerViewController itself, we then need to set up a few of its properties, and present it. This is to be put inside a function (e.g.     @IBAction func imageButtonDidPress(sender: AnyObject) {...} ).
```
            self.imagePicker.allowsEditing = true
            self.imagePicker.sourceType = .PhotoLibrary
            self.imagePicker.modalPresentationStyle = .Popover
            self.presentViewController(self.imagePicker, animated: true, completion: nil)
```

# UIActionSheet
The pop-up that comes from the button of the screen is a kind of UIAlertController. It is called an action sheet. 

```
        //show the action sheet (i.e. the little pop-up box from the bottom that allows you to choose whether you want to pick a photo from the photo library or from your camera)
        
        let optionMenu = UIAlertController(title: nil, message: "Where would you like the image from?", preferredStyle: UIAlertControllerStyle.ActionSheet)
        
        let photoLibraryOption = UIAlertAction(title: "Photo Library", style: UIAlertActionStyle.Default, handler: { (alert: UIAlertAction!) -> Void in
            print("from library")
        })
        let cameraOption = UIAlertAction(title: "Take a photo", style: UIAlertActionStyle.Default, handler: { (alert: UIAlertAction!) -> Void in
            print("take a photo")
            //shows the camera
            self.imagePicker.allowsEditing = true
            self.imagePicker.sourceType = .Camera
            self.imagePicker.modalPresentationStyle = .Popover
            self.presentViewController(self.imagePicker, animated: true, completion: nil)

        })
        let cancelOption = UIAlertAction(title: "Cancel", style: UIAlertActionStyle.Cancel, handler: {
            (alert: UIAlertAction!) -> Void in
            print("Cancel")
            self.dismissViewControllerAnimated(true, completion: nil)
        })
        
        //Adding the actions to the action sheet. Here, camera will only show up as an option if the camera is available in the first place.
        optionMenu.addAction(photoLibraryOption)
        optionMenu.addAction(cancelOption)
        if UIImagePickerController.isSourceTypeAvailable(UIImagePickerControllerSourceType.Camera) == true {
            optionMenu.addAction(cameraOption)} else {
            print ("I don't have a camera.")
        }

        //Now that the action sheet is set up, we present it. 
        self.presentViewController(optionMenu, animated: true, completion: nil)
        
```

