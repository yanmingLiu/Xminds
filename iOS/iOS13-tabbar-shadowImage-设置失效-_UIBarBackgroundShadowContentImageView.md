
```
if #available(iOS 13.0, *) {
    let appearance = self.standardAppearance.copy()
    appearance.backgroundImage = UIColor.clear.toImage() ?? UIImage()
    appearance.shadowImage = UIColor.clear.toImage() ?? UIImage()
    appearance.shadowColor = .clear
    appearance.configureWithTransparentBackground()
    self.standardAppearance = appearance
} else {
    backgroundImage = UIColor.clear.toImage() ?? UIImage()
    shadowImage = UIColor.white.toImage() ?? UIImage()
    isTranslucent = true
    barTintColor = .clear
}
```
