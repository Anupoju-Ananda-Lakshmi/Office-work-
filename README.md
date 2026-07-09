private AttachmentRef createAttachment(String type, String fileName) {

    AttachmentRef attachment = new AttachmentRef();
    attachment.setAttachmentType(type);
    attachment.setFileName(fileName);

    return attachment;
}
