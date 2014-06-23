title: 'c# imageUtils'
date: 2014-06-06 00:03:47
categories:
   - c#
   - wpf
tags:
   - c#
   - wpf
---

  最近在做一个简单的图片等比缩放的功能，在网上搜了半天，代码倒是很多但是很少能够封装的和业务无关。
  帮助类直接上代码。<!--more-->
  ```c#
   /// <summary>
    /// ImageUtils帮助类
    /// </summary>
    public sealed class ImageUtils
    {
        /// <summary>
        /// 图片等比缩放
        /// </summary>
        /// <param name="originalImage">原图片</param>
        /// <param name="width">缩放后的宽</param>
        /// <param name="height">缩放后的高</param>
        /// <returns>缩放后的图片</returns>
        public static Image Resize(Image originalImage, int width, int height)
        {
            if (originalImage == null || width < 0 || height < 0)
            {
                throw new ArgumentException("参数不合法。originalImage is null or width<0 or height<0");
            }
            double originalWidth = originalImage.Width;
            double originalHeight = originalImage.Height;
            double targetWidth = 0.0, targetHeight = 0.0;
            if (originalWidth <= width && originalHeight <= height)
            {
                return originalImage;
            }
            if (width*1.0 /originalWidth < height*1.0 / originalHeight)
            {
                double rate = (width * 1.0 / originalWidth);
                targetWidth = width;
                targetHeight = rate * originalHeight;
            }
            else
            {
                targetHeight = height;
                targetWidth = height * 1.0 / originalHeight * originalWidth;
            }
            Image newImage = null;
            Graphics newGraphic = null;
            try
            {
                newImage = new Bitmap(Convert.ToInt32(Math.Round(targetWidth)), Convert.ToInt32(Math.Round(targetHeight)));
                newGraphic = Graphics.FromImage(newImage);
                newGraphic.InterpolationMode = InterpolationMode.HighQualityBicubic;
                newGraphic.SmoothingMode = SmoothingMode.HighQuality;
                newGraphic.Clear(Color.White);
                newGraphic.DrawImage(originalImage,
                    new System.Drawing.Rectangle(0, 0, newImage.Width, newImage.Height),
                    new System.Drawing.Rectangle(0, 0, originalImage.Width, originalImage.Height),
                    System.Drawing.GraphicsUnit.Pixel);
                byte[] bytes = ImageToByteArray(newImage);
                return ByteArrayToImage(bytes);
            }
            finally {
                if (newGraphic != null)
                {
                    newGraphic.Dispose();
                }
                if (newImage != null)
                {
                    newImage.Dispose();
                }
                originalImage.Dispose();
            }
        }

        /// <summary>
        /// BitmapImage To ByteArray
        /// </summary>
        /// <param name="bmp">bitMap</param>
        /// <returns>ByteArray</returns>
        public static byte[] BitmapImageToByteArray(BitmapImage bmp)
        {
            if (bmp == null)
            {
                throw new ArgumentException("参数不合法，bmp is null");
            }
            byte[] byteArray = null;
            JpegBitmapEncoder encoder = new JpegBitmapEncoder();
            encoder.Frames.Add(BitmapFrame.Create(bmp));
            Stream sMarket = new MemoryStream();
            encoder.Save(sMarket);
            if (sMarket != null && sMarket.Length > 0)
            {
                sMarket.Position = 0;
                using (BinaryReader br = new BinaryReader(sMarket))
                {
                    byteArray = br.ReadBytes((int)sMarket.Length);
                }

            }
            return byteArray;
        }

        /// <summary>
        /// ByteArray to BitmapImage
        /// </summary>
        /// <param name="bytes">byte array</param>
        /// <returns>BitmapImage</returns>
        public static BitmapImage ByteArrayToBitmapImage(byte[] bytes)
        {
            if (bytes == null || bytes.Length == 0)
            {
                throw new ArgumentException("参数不合法，bytes is null or empty");
            }
            BitmapImage bmp = new BitmapImage();
            bmp.BeginInit();
            bmp.StreamSource = new MemoryStream(bytes);
            bmp.EndInit();
            return bmp;
        }

        /// <summary>
        /// byte array to Image
        /// </summary>
        /// <param name="bytes">byte</param>
        /// <returns>bytes</returns>
        public static Image ByteArrayToImage(byte[] bytes)
        {
            if (bytes == null || bytes.Length == 0)
            {
                throw new ArgumentException("参数不合法，bytes is null or empty");
            }
            Image image = null;
            using (Stream stream = new MemoryStream(bytes))
            {
                stream.Write(bytes, 0, bytes.Length);
                image = Image.FromStream(stream, true);
            }
            return image;
        }

        /// <summary>
        /// byte to Array
        /// </summary>
        /// <param name="image">image</param>
        /// <returns>byte array</returns>
        public static byte[] ImageToByteArray(Image image)
        {
            if (image == null)
            {
                throw new ArgumentException("参数不合法，image is null.");
            }
            byte[] bytes = null;
            using (Stream stream = new MemoryStream())
            {
                Bitmap bmp = new Bitmap(image);
                bmp.Save(stream, ImageFormat.Jpeg);
                bytes = new byte[stream.Length];
                stream.Position = 0;
                stream.Read(bytes, 0, Convert.ToInt32(bytes.Length));
            }
            return bytes;
        }
    }
  ```
  以上帮助类除了提供图片缩放功能之外，还提供了c#中各种Image的转换。
  Image=>BitmapImage 步骤：Image=>byte[]=>BitmapImage
  Stream=>Image  步骤：Stream=>byte[]=>Image
  BitmapImage=>Image 步骤：BitmapImage=>byte[]=>Image