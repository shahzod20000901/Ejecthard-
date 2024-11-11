https://www.iditect.com/faq/csharp/eject-usb-device-via-c.html
Mobile hard disk is USB interface。


  The following function can only eject the U disk, not the mobile hard disk。


    public static List<string> GetMoveDisk()
    {
        List<string> lstDisk = new List<string>();
        ManagementClass mgtCls = new ManagementClass("Win32_DiskDrive");
        var disks = mgtCls.GetInstances();
        foreach (ManagementObject mo in disks)
        {
            if (mo.Properties["MediaType"].Value == null ||
              mo.Properties["MediaType"].Value.ToString() != "External hard disk media")
            {
                continue;
            }

            foreach (ManagementObject diskPartition in mo.GetRelated("Win32_DiskPartition"))
            {
                foreach (var disk in diskPartition.GetRelated("Win32_LogicalDisk"))
                {
                    lstDisk.Add(disk.Properties["Name"].Value.ToString());
                }
            }
        }
        string filename = @"\\.\" + lstDisk[0];
        IntPtr handle = CreateFile(filename, GENERIC_READ | GENERIC_WRITE, FILE_SHARE_READ | FILE_SHARE_WRITE, IntPtr.Zero, 0x3, 0, IntPtr.Zero);

        uint byteReturned;
        bool result = Win32.DeviceIoControl(handle, IOCTL_STORAGE_EJECT_MEDIA, IntPtr.Zero, 0, IntPtr.Zero, 0, out byteReturned, IntPtr.Zero);
        bool isexit = Win32.CloseHandle(handle);
        return lstDisk;
    }
